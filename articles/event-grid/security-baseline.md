---
title: Azure-beveiligings basislijn voor Event Grid
description: De Event Grid Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5423c26b15c5ba9fa84e5d823f75f3c82a8cb8b4
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968115"
---
# <a name="azure-security-baseline-for-event-grid"></a>Azure-beveiligings basislijn voor Event Grid

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) Microsoft Azure Event grid. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Event grid. **Besturings elementen** die niet van toepassing zijn op Azure Event grid, zijn uitgesloten.

 
Als u wilt zien hoe Azure Event Grid volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Event grid beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: u kunt privé-eind punten gebruiken om direct vanuit uw virtuele netwerk toegang te geven tot uw event grid onderwerpen en domeinen veilig via een privé-verbinding zonder via het open bare Internet te gaan. Wanneer u een persoonlijk eind punt maakt voor uw Event Grid onderwerp of domein, biedt het een beveiligde verbinding tussen clients op uw VNet en uw Event Grid bron. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw virtuele netwerk. De verbinding tussen het persoonlijke eind punt en de Event Grid-Service maakt gebruik van een beveiligde persoonlijke koppeling.

Azure Event Grid biedt ook ondersteuning voor open bare IP-toegangs beheer voor het publiceren naar onderwerpen en domeinen. Met besturings elementen op basis van IP kunt u de uitgevers beperken tot een onderwerp of domein tot een set goedgekeurde computers en Cloud Services. Deze functie is een aanvulling op de verificatie mechanismen die door Event Grid worden ondersteund. 

- [Meer informatie over Event Grid privé-eind punten](./network-security.md#private-endpoints)

- [Meer informatie over Event Grid IP-firewall](./network-security.md#ip-firewall)

- [Azure Event Grid netwerk beveiliging](network-security.md) 

- [Overzicht van Azure Private Link](../private-link/private-link-overview.md)

- [Azure-netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw event grid resources in azure te beveiligen. Als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw Event Grid-bronnen, schakelt u de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzendt u logboeken naar een opslag account voor verkeers controle.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: u kunt IP-Firewall voor uw event grid-bron configureren om de toegang tot het open bare Internet te beperken tot alleen een select set van IP-adressen of IP-adresbereiken.

U kunt privé-eind punten zo configureren dat alleen toegang wordt beperkt tot geselecteerde virtuele netwerken.

Schakel DDoS Protection standaard in op deze virtuele netwerken om te beschermen tegen gedistribueerde Denial-of-service-aanvallen (DDoS). Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren. Raadpleeg voor meer informatie de volgende artikelen: 

- [Privé-eind punten voor Azure Event Grid onderwerpen of domeinen configureren](configure-private-endpoints.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Voor meer informatie over de Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw event grid-resources, schakelt u de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Opmerking: netwerk beleid is standaard uitgeschakeld wanneer er persoonlijke eind punten worden gemaakt voor Event Grid, dus mogelijk werkt de werk stroom niet.

Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Network Watcher pakket vastleggen in.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Selecteer een aanbieding op de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen en functies voor Payload-inspectie.  Als Payload-inspectie geen vereiste is, kan Azure Firewall Threat Intelligence worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren wordt gebruikt om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blok keren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen in virtuele netwerken die toegang nodig hebben tot uw Azure Event grid-resources, gebruikt u Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureEventGrid) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Het gebruik van de service label voor Azure Event Grid](./network-security.md#service-tags)

- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die zijn gekoppeld aan uw Azure Event grid-naam ruimten met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. EventGrid ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Event Grid resources te controleren of af te dwingen. 

U kunt ook gebruik maken van ingebouwde beleids definities die betrekking hebben op Azure Event Grid, zoals:

- Voor Azure Event Grid-domeinen moeten privékoppelingen worden gebruikt

- Azure Event Grid onderwerpen moeten persoonlijke koppelingen gebruiken
- [ingebouwd beleid voor Event Grid bronnen](../governance/policy/samples/built-in-policies.md#event-grid)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Azure Event grid resources om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op Azure Event grid. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Event grid. In de Azure Monitor gebruikt u Log Analytics werk ruimte (n) om een query uit te voeren en een analyse te verrichten en opslag accounts te gebruiken voor lange termijn/archiverings opslag. U kunt ook gegevens naar Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM) inschakelen en op de trein zetten.

- [Diagnostische logboeken inschakelen voor Azure Event Grid](diagnostic-logs.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: met Diagnostische instellingen kunnen Event grid gebruikers fouten logboeken voor publiceren en verzenden vastleggen en weer geven in een opslag account, een event hub of een log Analytics-werk ruimte.

- [Diagnostische logboeken inschakelen voor Azure Event grid-onderwerpen of-domeinen](enable-diagnostic-logs-topic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure Event grid bronnen volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en de resultaten van Azure Event grid regel matig bekijken. Gebruik Azure Monitor en een Log Analytics werk ruimte om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische logboeken inschakelen voor Azure Event grid-onderwerpen of-domeinen](enable-diagnostic-logs-topic.md)

- [Query's uitvoeren voor Azure Event Grid in Log Analytics-werk ruimten](diagnostic-logs.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics query's](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Stel Diagnostische instellingen in het gebeurtenis raster in voor toegang tot publicatie-en leverings fout Logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. U kunt de logboeken verzenden naar een Log Analytics-werk ruimte. Gebruik Azure Security Center met Log Analytics voor bewaking en waarschuwingen voor afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden. 

U kunt ook waarschuwingen maken voor Azure Event Grid metrische gegevens en activiteiten logboek bewerkingen. U kunt waarschuwingen maken voor zowel de metrische gegevens voor publiceren als levering voor Azure Event Grid resources (onderwerpen en domeinen). 

Daarnaast kunt u uw Log Analytics-werk ruimte onboarden naar Azure Sentinel, omdat deze een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.

- [Diagnostische logboeken inschakelen voor Azure Event Grid onderwerpen of domeinen](enable-diagnostic-logs-topic.md)

- [Waarschuwingen instellen voor Azure Event Grid metrische gegevens en activiteiten logboeken](set-alerts.md)

- [Details van Event Grid diagnostische logboek schema](diagnostic-logs.md)

- [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-log.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Event Grid geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Event Grid worden geen DNS-gerelateerde logboeken verwerkt of geproduceerd.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: met Azure Event grid kunt u het toegangs niveau dat aan verschillende gebruikers wordt gegeven, beheren om verschillende beheer bewerkingen uit te voeren, zoals abonnementen op lijst gebeurtenissen, nieuwe maken en sleutels genereren. Event Grid maakt gebruik van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Event Grid ondersteunt ingebouwde rollen en aangepaste rollen.

Met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal.

- [Toegang tot Event Grid-resources autoriseren](security-authorization.md)

- [Een directory-rol verkrijgen in Azure Active Directory (Azure AD) met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: toegangs beheer voor Event grid bronnen wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaard wachtwoorden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts.

U kunt ook Just-in-time-toegang inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management en Azure Resource Manager.

Event Grid kunt een beheerde service-identiteit voor Azure Event grid-onderwerpen of-domeinen inschakelen en deze gebruiken om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus wacht rijen en onderwerpen, Event hubs en opslag accounts. Shared Access Signature-token (SAS) wordt gebruikt voor het publiceren van gebeurtenissen naar Azure Event Grid. Maak een standaard procedure voor het maken van toegang tot en het door sturen en publiceren van gebeurtenissen met deze accounts.

- [Gebeurtenis levering verifiëren naar gebeurtenis-handlers (Azure Event Grid)](security-authentication.md)

- [Publicerend clients verifiëren (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Meer informatie over Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: niet van toepassing; SSO wordt niet ondersteund door de Event Grid-Service.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: niet van toepassing; Event Grid-Service maakt geen gebruik van multi-factor Authentication.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: niet van toepassing; voor geen enkele Event Grid-scenario's zijn werk stations met verhoogde toegang vereist.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) beveiligings rapporten en-bewaking om te detecteren wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Richt lijnen**: niet van toepassing. Event Grid maakt geen gebruik van Azure Active Directory (Azure AD) voor het verifiëren van clients voor het publiceren van gebeurtenissen. het ondersteunt verificatie via SAS-sleutels.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Event Grid kunt een beheerde service-identiteit voor Azure Event grid-onderwerpen of-domeinen inschakelen en deze gebruiken om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus wacht rijen en onderwerpen, Event hubs en opslag accounts. Shared Access Signature-token (SAS) wordt gebruikt voor het publiceren van gebeurtenissen naar Azure Event Grid. 

- [Gebeurtenis levering verifiëren naar gebeurtenis-handlers (Azure Event Grid)](security-authentication.md)

- [Publicerend clients verifiëren (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure AD Identity and Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Gebruik Azure AD Privileged Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Azure AD Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot Azure Active Directory (Azure AD) aanmeldings activiteiten, controle en risico logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

- [Azure-activiteiten logboeken integreren met Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory-functies (Azure AD) voor identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing; Event Grid-Service biedt momenteel geen ondersteuning voor Klanten-lockbox.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.
 
 
 
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure-resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure-resources beheren via Azure RBAC.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle inhoud van de klant als gevoelig en gaat het om tot een uitstekende lengte van het verlies en de bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: voor Azure Event grid is https vereist voor het publiceren en ondersteunt https voor het leveren van gebeurtenissen aan een webhook-eind punt. In azure Global ondersteunt Event Grid zowel 1,1-als 1,2-versies van TLS, maar we raden u ten zeerste aan de 1,2-versie te gebruiken. In nationale Clouds, zoals Azure Government en Azure geëxploiteerd door 21Vianet in China, ondersteunt Event Grid alleen 1,2-versie van TLS.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Event grid. Implementeer, indien nodig, een oplossing van derden voor naleving.

Voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle inhoud van de klant als gevoelig en gaat het om tot een uitstekende lengte van het verlies en de bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Azure Event grid ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen voor Event grid bronnen goed te keuren. Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een beveiligingsprincipal, die een gebruiker of een service-principal van de toepassing is.

- [Toegang tot Event Grid-resources autoriseren](security-authorization.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in productie-exemplaren van Azure Event grid resources en andere kritieke of verwante resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.
 
 
 
- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources conform uw organisatie behoeften.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik Azure Active Directory (Azure AD) voorwaardelijke toegang om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [ Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure Event grid-service met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. EventGrid ' om aangepaste beleids regels te maken om de configuratie van uw Azure Event Grid Services te controleren of af te dwingen.

Azure Resource Manager kunt de sjabloon in JavaScript Object Notation (JSON) exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie voordat ze worden geïmplementeerd.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources. Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw event grid of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. EventGrid ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans voor uw Azure-resources uit te voeren. Gebruik Azure Policy bovendien om een waarschuwing te krijgen en Azure-resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Verantwoordelijkheid**: niet van toepassing

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Event grid gebruikt het Shared Access Signature SAS-token voor het publiceren van gebeurtenissen voor het event grid van onderwerpen of domeinen. Het genereren van SAS-tokens met alleen toegang tot de resources die nodig zijn in een beperkt tijd venster.

Gebruik beheerde identiteiten in combi natie met Azure Key Vault om het geheime beheer van uw Cloud toepassingen te vereenvoudigen.

- [Publicerend clients verifiëren (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Beheerde identiteiten gebruiken voor Azure-resources](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: Event grid kunt een beheerde service-identiteit inschakelen voor Azure Event grid-onderwerpen of-domeinen. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus-wacht rijen en-onderwerpen, Event hubs en opslag accounts.

- [Gebeurtenis levering met een beheerde identiteit](managed-service-identity.md)

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

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Event Grid), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: Event Grid heeft een automatische geo-nood herstel (GeoDR) van meta gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenis abonnementen. Als een hele Azure-regio uitvalt, worden er al met de meta gegevens van de infra structuur van uw gebeurtenis een gepaarde regio gesynchroniseerd met Event Grid.

- [Geografisch nood herstel aan de server zijde in Azure Event Grid](geo-disaster-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: Event Grid heeft een automatische geo-nood herstel (GeoDR) van meta gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenis abonnementen. Als een hele Azure-regio uitvalt, worden er al met de meta gegevens van de infra structuur van uw gebeurtenis een gepaarde regio gesynchroniseerd met Event Grid.

Event Grid ondersteunt momenteel geen door de klant beheerde sleutels. 

- [Geografisch nood herstel aan de server zijde in Azure Event Grid](geo-disaster-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Event Grid heeft een automatische geo-nood herstel (GeoDR) van meta gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenis abonnementen. Als een hele Azure-regio uitvalt, worden er al met de meta gegevens van de infra structuur van uw gebeurtenis een gepaarde regio gesynchroniseerd met Event Grid.

Event Grid ondersteunt momenteel geen door de klant beheerde sleutels. 

- [Geografisch nood herstel aan de server zijde in Azure Event Grid](geo-disaster-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel de beveiliging van zacht verwijderen en leegmaken in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. 
 

Event Grid ondersteunt momenteel geen door de klant beheerde sleutels. 

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident. 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke intentie is achter de activiteit die tot de waarschuwing heeft geleid.

 
 
 
 Markeer bovendien abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig vervolgens uw reactie schema naar wens.
 
 
 
- [ Publicatie van het NIST-hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.
 
 
 
- [ De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: werk stroom automatisering gebruiken Azure Security Center om automatisch reacties te activeren op beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering configureren in Security Center](../security-center/workflow-automation.md)

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
