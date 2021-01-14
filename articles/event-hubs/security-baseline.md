---
title: Azure-beveiligings basislijn voor Event Hubs
description: Azure-beveiligings basislijn voor Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3f56fc1b321396c5cc6aa93e3f47fcaafcbe4ae4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202331"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-beveiligings basislijn voor Event Hubs

De Azure-beveiligings basislijn voor Event Hubs bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Richt lijnen**: de integratie van Event hubs met virtuele netwerk service-eind punten maakt veilige toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Nadat het service-eind punt van ten minste één subnet van een virtueel netwerk is gebonden, accepteert de respectieve Event Hubs naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar gemachtigd subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk moet uw Event Hubs naam ruimte binden aan een service-eind punt een geïsoleerde netwerk tunnel van het subnet van het virtuele netwerk naar de berichten service configureren. 

U kunt ook een persoonlijk eind punt maken. Dit is een netwerk interface die u privé en veilig met Azure Event Hubs service verbindt met behulp van de persoonlijke koppelings service van Azure. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. 

U kunt uw Azure Event Hubs-naam ruimte ook beveiligen met behulp van firewalls. Azure Event Hubs ondersteunt toegangs beheer op basis van IP voor binnenkomende firewall ondersteuning. U kunt Firewall regels instellen met behulp van de Azure Portal, Azure Resource Manager sjablonen of via de Azure CLI of Azure PowerShell.

Service-eind punten voor virtuele netwerken gebruiken met Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Zie Azure Event Hubs integreren met persoonlijke Azure-koppeling voor meer informatie: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Integratie van virtuele netwerken en firewalls inschakelen op Event Hubs naam ruimte: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

IP-firewall regels configureren voor Azure Event Hubs-naam ruimten: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw event hubs resources in azure te beveiligen. Als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw event hubs, schakelt u de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzendt u logboeken naar een opslag account voor verkeers controle.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informatie over netwerk beveiliging die wordt verschaft door Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Protection standaard in voor de virtuele netwerken die zijn gekoppeld aan uw event hubs om te beschermen tegen gedistribueerde Denial-of-service-aanvallen (DDoS). Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

DDoS-beveiliging configureren: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

Voor meer informatie over de Azure Security Center geïntegreerde bedreigings informatie: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Richt lijnen**: als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw event hubs, schakelt u de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Network Watcher pakket vastleggen in.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Network Watcher inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw event hubs, selecteert u een aanbieding op de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden. Als inbraak detectie en/of preventie op basis van Payload-inspectie niet vereist is voor uw organisatie, kunt u de functie ingebouwde firewall van Azure Event Hubs gebruiken. U kunt de toegang tot uw Event Hubs-naam ruimte beperken voor een beperkt bereik van IP-adressen of een specifiek IP-adres met behulp van firewall regels.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Een firewall regel toevoegen in Event Hubs voor een opgegeven IP-adres:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center bewaking**: nog niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: niet van toepassing. deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure app service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk resources die zijn gekoppeld aan uw Azure Event hubs-naam ruimten met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. EventHub ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Event Hubs naam ruimten te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot Azure Event Hubs, zoals:

- Event hub moet een service-eind punt voor een virtueel netwerk gebruiken.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ingebouwd beleid voor Azure voor Event Hubs naam ruimte:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy voor beelden voor netwerken: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor virtuele netwerken en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw event hubs.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op Azure Event hubs. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: niet van toepassing; Micro soft onderhoudt de tijd bronnen die worden gebruikt voor Azure-resources, zoals Azure Event Hubs, voor tijds tempels in de logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: in azure monitor kunt u logboeken met betrekking tot Event hubs in het activiteiten logboek en de diagnostische instellingen van Event hub configureren om logboeken te verzenden naar een log Analytics-werk ruimte om te worden gevraagd of in een opslag account voor langdurige archiverings opslag.

Diagnostische instellingen configureren voor Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Informatie over Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Diagnostische instellingen inschakelen voor uw Azure Event hubs-naam ruimte. Er zijn drie categorieën Diagnostische instellingen voor Azure Event Hubs: Archief logboeken, operationele logboeken en logboeken voor automatisch schalen. Schakel operationele Logboeken in om informatie vast te leggen over wat er gebeurt tijdens Event Hubs bewerkingen, met name het bewerkings type, inclusief Event Hub maken, gebruikte resources en de status van de bewerking.

Daarnaast kunt u Diagnostische instellingen voor Azure-activiteiten logboek inschakelen en deze verzenden naar een Azure Storage-account, Event Hub of een Log Analytics-werk ruimte. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-Event Hubs en andere resources. Met activiteiten Logboeken kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn gemaakt op uw Azure Event Hubs-naam ruimten.

Diagnostische instellingen inschakelen voor Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Diagnostische instellingen inschakelen voor Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings regels van uw organisatie om event hub-gerelateerde incidenten vast te leggen en te controleren.

Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Analyseer en bewaak logboeken voor afwijkend gedrag en controleer regel matig de resultaten die betrekking hebben op uw event hubs. Gebruik de Log Analytics van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.
 

Voor meer informatie over de Log Analytics-werk ruimte: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Richt lijnen**: in azure monitor kunt u logboeken met betrekking tot Azure-Event hubs in het activiteiten logboek configureren en Event hubs Diagnostische instellingen voor het verzenden van logboeken naar een log Analytics werk ruimte om te worden gevraagd of in een opslag account voor langdurige archiverings opslag. Gebruik Log Analytics werk ruimte om waarschuwingen te maken voor afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden.

U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten. 

Meer informatie over het Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Diagnostische instellingen configureren voor Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Waarschuwing over Log Analytics werkruimte logboek gegevens: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center bewaking**: nog niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Event hub verwerkt geen anti-malware-logboek registratie.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Event Hubs verwerkt of produceert geen aan DNS gerelateerde Logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen. 

Een directory-rol verkrijgen in azure AD met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directory-rol in azure AD ophalen met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: de toegang tot Event hubs beheren wordt geregeld via Azure Active Directory (AD). Azure AD heeft niet het concept van standaard wachtwoorden.

De toegang tot het gegevens vlak tot Event Hubs wordt beheerd via Azure AD met beheerde identiteiten of App-registraties, evenals hand tekeningen voor gedeelde toegang. Shared Access signatures worden gebruikt door de clients die verbinding maken met uw event hubs en kunnen op elk gewenst moment opnieuw worden gegenereerd.

Informatie over gedeelde toegangs handtekeningen voor Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Azure Security Center gebruiken om identiteit en toegang te bewaken (preview): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Policy gebruiken: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: Microsoft Azure biedt geïntegreerd beheer van toegangs beheer voor bronnen en toepassingen op basis van Azure Active Directory (AD). Een belang rijk voor deel van het gebruik van Azure AD met Azure Event Hubs is dat u uw referenties niet meer hoeft op te slaan in de code. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. De resource naam voor het aanvragen van een token is https: \/ /eventhubs.Azure.net/. Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD een toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om een aanvraag voor Azure Event Hubs-resources te autoriseren.

Een toepassing verifiëren met Azure AD voor toegang tot Event Hubs resources: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Informatie over eenmalige aanmelding met Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: schakel Azure Active Directory multi-factor Authentication (MFA) in en volg de aanbevelingen voor Azure Security Center identiteits-en toegangs beheer om uw resources met een gebeurtenis hub te beveiligen.

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw (privileged Access workstations) met multi-factor Authentication (MFA) die zijn geconfigureerd voor aanmelding bij en configureren van bronnen die met Event hub zijn ingeschakeld.

Meer informatie over privileged Access workstations: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. Voor aanvullende logboek registratie kunt u waarschuwingen voor Azure Security Center risico detectie verzenden naar Azure Monitor en aangepaste waarschuwingen/meldingen configureren met actie groepen.

Privileged Identity Management (PIM) implementeren: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Meer informatie over Azure AD-risico detectie: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Actie groepen configureren voor aangepaste waarschuwingen en meldingen: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.



Benoemde locaties configureren in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor Azure-resources, zoals Event hubs. Hiermee kunnen op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure worden uitgevoerd voor beheer gevoelige bronnen.

 Een Azure AD-exemplaar maken en configureren: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Zie toegang tot Event Hubs resources toestaan met Azure Active Directory voor meer informatie over de manier waarop Azure Event Hubs integreert met Azure Active Directory (AAD): https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Draai de hand tekeningen voor gedeelde toegang van uw Event Hubs regel matig.

Meer informatie over Azure AD Reporting: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access revisies gebruiken: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Informatie over gedeelde toegangs handtekeningen voor Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: u hebt toegang tot Azure Active Directory (AD) aanmeldings activiteiten, controle en risico logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

Azure-activiteiten logboeken integreren in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik de functies voor identiteits beveiliging en risico detectie van Azure Active Directory om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op uw event hubs-resources. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren.

Hoe kan ik Risk ante aanmeldingen voor Azure AD bekijken: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: momenteel niet beschikbaar; Klanten-lockbox wordt nog niet ondersteund voor Event Hubs.

Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: momenteel niet beschikbaar

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Gebruik labels op resources die betrekking hebben op uw event hubs voor het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Event Hubs naam ruimten moeten worden gescheiden door een virtueel netwerk waarbij service-eind punten zijn ingeschakeld en op de juiste wijze worden gelabeld.

U kunt uw Azure Event Hubs-naam ruimte ook beveiligen met behulp van firewalls. Azure Event Hubs ondersteunt toegangs beheer op basis van IP voor binnenkomende firewall ondersteuning. U kunt Firewall regels instellen met behulp van de Azure Portal, Azure Resource Manager sjablonen of via de Azure CLI of Azure PowerShell.

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

IP-firewall regels configureren voor Azure Event Hubs-naam ruimten: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een Virtual Network maken: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: wanneer u virtuele machines gebruikt om toegang te krijgen tot uw event hubs, maakt u gebruik van virtuele netwerken, service-eind punten, Event hubs firewall, netwerk beveiligings groepen en service tags om de kans op gegevens exfiltration te beperken.

Micro soft beheert de onderliggende infra structuur voor Azure Event Hubs en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

IP-firewall regels configureren voor Azure Event Hubs-naam ruimten: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Meer informatie over Virtual Network Service-eind punten met Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Azure-Event Hubs integreren met persoonlijke Azure-koppeling: https://docs.microsoft.com/azure/event-hubs/private-link-service

Meer informatie over netwerk beveiligings groepen en service Tags: https://docs.microsoft.com/azure/virtual-network/security-overview

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: in azure Event hubs worden standaard TLS-versleutelde communicatie afgedwongen. TLS-versies 1,0, 1,1 en 1,2 worden momenteel ondersteund. TLS 1,0 en 1,1 bevinden zich echter op een pad naar de hele industrie, dus gebruik TLS 1,2 als dat mogelijk is.

Zie netwerk beveiliging voor informatie over de beveiligings functies van Event Hubs:  https://docs.microsoft.com/azure/event-hubs/network-security

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Event hubs. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Azure Event hubs ondersteunt het gebruik van Azure Active Directory (AD) om aanvragen voor Event hubs bronnen te autoriseren. Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een beveiligingsprincipal, die een gebruiker of een service-principal van de toepassing is.

Meer informatie over Azure RBAC en de beschik bare rollen voor Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Event Hubs en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Event hubs ondersteunt de mogelijkheid om gegevens in rust te versleutelen met door micro soft beheerde sleutels of door de klant beheerde sleutels. Met deze functie kunt u toegang tot de door de klant beheerde sleutels maken, draaien, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Event Hubs-gegevens in rust.

Door de klant beheerde sleutels voor het versleutelen van Azure-Event Hubs configureren: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure Event hubs en andere essentiële of gerelateerde resources.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Event Hubs.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; Micro soft voert patch beheer uit op de onderliggende systemen die ondersteuning bieden voor Event Hubs.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Event Hubs.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Event Hubs.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief Azure Event hubs-naam ruimten) binnen uw abonnement (en) te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Query's maken met Azure resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Meer informatie over Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure Event hubs-naam ruimten en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken bronnen en Azure als geheel.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast kunt u met Azure resource Graph bronnen in de abonnementen opvragen/ontdekken.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken bronnen en Azure als geheel.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

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

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken</div>

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure Event hubs-implementaties. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. EventHub ' om aangepaste beleids regels te maken om configuraties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Event Hubs, zoals:

- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld

- Event Hub moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

Ingebouwd beleid voor Azure voor Event Hubs naam ruimte: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Beschik bare Azure Policy aliassen weer geven: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw event hubs-resources. 

Azure Policy configureren en beheren:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Voor meer informatie over de Azure Policy effecten:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw event hubs of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. EventHub ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. EventHub ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure Event Hubs-implementaties en gerelateerde resources.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor virtuele machines van Azure of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw event hubs, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van de hand tekening van gedeelde toegang te vereenvoudigen en te beveiligen voor uw Azure Event hubs-implementaties. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Verifieer een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Door de klant beheerde sleutels voor Event Hubs configureren: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Integratie met door Azure beheerde identiteiten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw event hubs, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om Azure Event hubs te vereenvoudigen en te beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Key Vault, zonder enige referenties in uw code.

Verifieer een beheerde identiteit met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Door de klant beheerde sleutels voor Event Hubs configureren: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

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

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: het vooraf scannen van inhoud die wordt geüpload naar niet-reken resources van Azure, zoals Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure database for PostgreSQL, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure cache voor redis), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp** bij het configureren van geo-nood herstel voor Azure Event hubs. Als er voor de hele Azure-regio's of-Data Centers (als er geen beschikbaarheids zones worden gebruikt) downtime actief is, is het van essentieel belang dat de gegevens worden verwerkt in een andere regio of Data Center. Daarom zijn geo-nood herstel en geo-replicatie belang rijke functies voor elke onderneming. Azure Event Hubs ondersteunt zowel geo-nood herstel als geo-replicatie op het niveau van de naam ruimte. 

Meer informatie over geo-nood herstel voor Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: Azure Event hubs zorgt voor versleuteling van gegevens in rust met Azure Storage-service versleuteling (Azure SSE). Event Hubs is afhankelijk van Azure Storage om de gegevens op te slaan en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door micro soft beheerde sleutels. Als u Azure Key Vault gebruikt voor het opslaan van door de klant beheerde sleutels, zorgt u voor regel matige automatische back-ups van uw sleutels.

Zorg voor regel matige automatische back-ups van uw Key Vault geheimen met de volgende Power shell-opdracht: Backup-AzKeyVaultSecret

Door de klant beheerde sleutels voor het versleutelen van Azure Event Hubs-gegevens op rest configureren: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Back-ups maken van Key Vault geheimen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: het herstellen van back-ups van door de klant beheerde sleutels testen.

 

Sleutel kluis sleutels herstellen in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel zacht verwijderen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. Azure Event Hubs vereist dat door de klant beheerde sleutels worden verwijderd en niet zijn geconfigureerd.

Configureer zacht verwijderen voor Azure Storage account dat wordt gebruikt voor het vastleggen van Event Hubs gegevens. Houd er rekening mee dat deze functie nog niet wordt ondersteund voor Azure Data Lake Storage gen 2.

Het inschakelen van zacht verwijderen in Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Stel een sleutel kluis in met sleutels: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Zacht verwijderen voor Azure Storage-blobs: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richt lijnen**: Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die rollen van personeel en fasen van incident handling/Management definiëren.

Werk stroom automatisering configureren in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

**Monitoring door Azure Security Center**: Ja

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

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen, hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)