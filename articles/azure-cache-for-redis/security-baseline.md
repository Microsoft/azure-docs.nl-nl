---
title: Azure-beveiligings basislijn voor Azure cache voor redis
description: Azure-beveiligings basislijn voor Azure cache voor redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 567652ec8da257e72aafa95e24af9f5680a2b6d3
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807951"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-beveiligings basislijn voor Azure cache voor redis

De Azure-beveiligings basislijn voor Azure cache voor redis bevat aanbevelingen waarmee u de beveiligings-postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Richt lijnen**: Implementeer uw Azure-cache voor redis-exemplaar binnen een virtueel netwerk (VNet). Een VNet is een privé netwerk in de Cloud. Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een VNet, is het niet openbaar adresseerbaar en is deze alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.

U kunt ook firewall regels met een begin-en eind-IP-adres bereik opgeven. Wanneer de firewall regels zijn geconfigureerd, kunnen alleen client verbindingen van de opgegeven IP-adresbereiken verbinding maken met de cache.

Virtual Network ondersteuning configureren voor een Premium Azure-cache voor redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure-cache configureren voor redis-firewall regels:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: wanneer virtual machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure-cache voor redis-exemplaar, kunt u netwerk beveiligings groepen (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: Azure Virtual Network (VNet)-implementatie biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, Toegangs beheer beleid en andere functies om de toegang verder te beperken. Bij implementatie in een VNet is Azure cache voor redis niet openbaar adresseerbaar en kan alleen worden geopend vanaf virtuele machines en toepassingen binnen het VNet.

Schakel DDoS Protection standaard in op de VNets die is gekoppeld aan uw Azure-cache voor redis-exemplaren om te beschermen tegen gedistribueerde Denial-of-service-aanvallen (DDoS). Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

Virtual Network ondersteuning configureren voor een Premium Azure-cache voor redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure DDoS Protection standaard beheren met de Azure Portal:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Richt lijnen**: wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure-cache voor redis-exemplaar, kunt u netwerk beveiligings groepen (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: wanneer u Azure cache gebruikt voor redis met uw webtoepassingen die worden uitgevoerd op Azure app service-of COMPUTE-instanties, implementeert u alle resources in een Azure Virtual Network (VNet) en kunt u beveiligen met een Azure Web Application firewall (WAF) op Internet Application Gateway. Configureer de WAF om uit te voeren in de modus preventie. In de modus voor preventie worden indringing en aanvallen die door de regels worden gedetecteerd geblokkeerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

U kunt ook een aanbieding uit de Azure Marketplace selecteren die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie en/of anomalie detectie mogelijkheden.

Meer informatie over Azure WAF-mogelijkheden:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Azure WAF implementeren:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen (NSG) of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

U kunt ook toepassings beveiligings groepen (ASG) gebruiken om complexe beveiligings configuratie te vereenvoudigen. Met Asg's kunt u netwerk beveiliging configureren als een natuurlijke uitbrei ding van de structuur van een toepassing, zodat u virtuele machines kunt groeperen en netwerk beveiligings beleid kunt definiëren op basis van die groepen.

Service tags van virtueel netwerk:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Toepassings beveiligings groepen:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die betrekking hebben op uw Azure-cache voor redis-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. cache ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-cache te controleren of af te dwingen voor redis-exemplaren. U kunt ook gebruik maken van ingebouwde beleids definities zoals:

Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

De DDoS Protection-standaard moet zijn ingeschakeld

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager (ARM)-Sjablonen, Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en beleids regels, in één blauw druk te definiëren. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Azure-cache voor redis-implementatie om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik het Azure-activiteiten logboek om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure-cache voor redis-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure cache, voor redis voor tijds tempels in de logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Met activiteiten Logboeken kunt u inzicht krijgen in de bewerkingen die zijn uitgevoerd op uw Azure-cache voor redis-exemplaren op het niveau van het besturings vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings vlak voor uw Azure-cache voor redis-exemplaren.

Diagnostische instellingen inschakelen voor Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Met activiteiten Logboeken kunt u inzicht krijgen in de bewerkingen die zijn uitgevoerd op uw Azure-cache voor redis-exemplaren op het niveau van het besturings vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings vlak voor uw Azure-cache voor redis-exemplaren.

Als er metrische gegevens beschikbaar zijn door Diagnostische instellingen in te scha kelen, is controle logboek registratie op het gegevensvlak nog niet beschikbaar voor Azure-cache voor redis.

Diagnostische instellingen inschakelen voor Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor logboek registratie In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure-cache voor redis-instanties volgens de nalevings voorschriften van uw organisatie.

Houd er rekening mee dat audit logboek registratie op het gegevens vlak nog niet beschikbaar is voor Azure cache voor redis.

Para meters voor het bewaren van Logboeken instellen:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte. Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure cache voor redis.

Houd er rekening mee dat audit logboek registratie op het gegevens vlak nog niet beschikbaar is voor Azure cache voor redis.

Diagnostische instellingen inschakelen voor Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Richt lijnen**: u kunt configureren voor het ontvangen van waarschuwingen op basis van metrische gegevens en activiteiten logboeken die betrekking hebben op uw Azure-cache voor redis-exemplaren. Met Azure Monitor kunt u een waarschuwing configureren voor het verzenden van een e-mail melding, het aanroepen van een webhook of het aanroepen van een Azure Logic-app.

Als er metrische gegevens beschikbaar zijn door Diagnostische instellingen in te scha kelen, is controle logboek registratie op het gegevensvlak nog niet beschikbaar voor Azure-cache voor redis.

Waarschuwingen voor Azure cache configureren voor redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure cache voor redis verwerkt of produceert geen anti-malware-gerelateerde Logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure cache voor redis verwerkt of produceert geen aan DNS gerelateerde Logboeken.

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

**Hulp**: de toegang tot Azure-cache beheren voor redis wordt beheerd via Azure Active Directory (AD). Azure AD heeft niet het concept van standaard wachtwoorden. 

Toegang tot het gegevens vlak voor Azure cache voor redis wordt beheerd via toegangs sleutels. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache en kunnen op elk gewenst moment opnieuw worden gegenereerd.

Het is niet raadzaam om standaard wachtwoorden te bouwen in uw toepassing. In plaats daarvan kunt u uw wacht woorden opslaan in Azure Key Vault en vervolgens Azure Active Directory gebruiken om deze op te halen.

Azure-cache opnieuw genereren voor de toegangs sleutels voor redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

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

**Richt lijnen**: Azure cache voor redis maakt gebruik van toegangs sleutels om gebruikers te verifiëren en biedt geen ondersteuning voor eenmalige aanmelding (SSO) op het niveau van het gegevens vlak. Toegang tot het besturings vlak voor Azure cache voor redis is beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token dat u hebt verkregen via Azure Active Directory.

Meer informatie over Azure cache voor redis REST API: https://docs.microsoft.com/rest/api/redis/

Meer informatie over eenmalige aanmelding met Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (AD) multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication (MFA) die zijn geconfigureerd voor aanmelding bij en configureren van Azure-resources.

Meer informatie over privileged Access workstations:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

Privileged Identity Management (PIM) implementeren: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Meer informatie over Azure AD-risico detectie: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: benoemde locaties in azure Active Directory (AD) voorwaardelijke toegang configureren om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Azure AD-verificatie kan niet worden gebruikt voor directe toegang tot Azure cache voor redis gegevens vlak. Azure AD-referenties kunnen echter worden gebruikt voor beheer op het niveau van het besturings vlak (dat wil zeggen de Azure Portal) om Azure-cache te beheren voor redis-toegangs sleutels.


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Meer informatie over Azure AD Reporting: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access revisies gebruiken: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Richt lijnen**: u hebt toegang tot Azure Active Directory (AD) aanmeldings activiteiten, audits en risico logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een Siem van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

Azure-activiteiten logboeken integreren in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

De Azure-Sentinel op het bord: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak, gebruikt u Azure Active Directory (AD) identiteits beveiliging en risico detectie functies voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

Hoe kan ik Risk ante aanmeldingen voor Azure AD bekijken: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: nog niet beschikbaar; Klanten-lockbox wordt nog niet ondersteund voor Azure cache voor redis.

Lijst met door Klanten-lockbox ondersteunde services:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure cache voor redis-exemplaren moet worden gescheiden door het virtuele netwerk/subnet en op de juiste wijze worden gelabeld. Gebruik eventueel de Azure cache voor redis-firewall om regels te definiëren zodat alleen client verbindingen van opgegeven IP-adresbereiken verbinding kunnen maken met de cache.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Azure-cache implementeren voor redis in een Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure-cache configureren voor redis-firewall regels:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: nog niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure-cache voor redis.

Micro soft beheert de onderliggende infra structuur voor Azure cache voor redis en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Azure-cache voor redis vereist standaard TLS-versleutelde communicatie. TLS-versies 1,0, 1,1 en 1,2 worden momenteel ondersteund. TLS 1,0 en 1,1 bevinden zich echter op een pad naar de hele industrie, dus gebruik TLS 1,2 als dat mogelijk is. Als uw client bibliotheek of-hulp programma geen TLS ondersteunt, kunnen niet-versleutelde verbindingen worden uitgevoerd via de Azure Portal-of beheer-Api's. In dergelijke gevallen is het raadzaam om uw cache-en client toepassing in te zetten in een virtueel netwerk.

Meer informatie over versleuteling in transit voor Azure cache voor redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Meer informatie over de vereiste poorten die worden gebruikt in Vnet-cache scenario's:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure-cache voor redis. Label instanties die gevoelige informatie bevatten als zodanig en implementeren van een oplossing van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot de Azure-cache te beheren voor redis-besturings vlak (dat wil zeggen Azure Portal). 

Azure RBAC configureren:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Azure cache voor redis en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: Azure cache voor redis slaat klant gegevens op in het geheugen en is volledig beveiligd door veel besturings elementen die door micro soft worden geïmplementeerd. het geheugen wordt standaard niet versleuteld. Als uw organisatie dit vereist, versleutelt u de inhoud voordat u deze opslaat in azure cache voor redis.

Als u de Azure-cache gebruikt voor redis-functie ' redis data Persistence ', worden gegevens verzonden naar een Azure Storage account dat u bezit en beheert. U kunt persistentie configureren van de Blade ' nieuwe Azure-cache voor redis ' tijdens het maken van de cache en in het resource menu voor bestaande Premium-caches.

Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling kan niet worden uitgeschakeld. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.

Persistentie configureren in azure cache voor redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Informatie over versleuteling voor Azure Storage accounts: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Meer informatie over Azure-beveiliging van klant gegevens: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure cache voor redis en andere kritieke of gerelateerde resources.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg de aanbevelingen van Azure Security Center over het beveiligen van uw Azure-cache voor redis-instanties en gerelateerde bronnen.

Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure cache voor redis.

Meer informatie over Azure Security Center-aanbevelingen: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure cache voor redis.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Meer informatie over Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-cache in te delen en bij te houden voor redis-exemplaren en gerelateerde bronnen. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

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

Niet toegestane resourcetypen

Toegestane brontypen

Daarnaast kunt u met Azure resource Graph bronnen in de abonnementen opvragen/ontdekken.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

Niet toegestane resourcetypen

Toegestane brontypen

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager (arm) te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

Voorwaardelijke toegang configureren om toegang tot ARM te blok keren:

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

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure-cache voor redis-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. cache ' om aangepaste beleids regels te maken om de configuratie van uw Azure-cache voor redis-instanties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw Azure-cache voor redis-instanties, zoals:

Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

Beschik bare Azure Policy aliassen weer geven: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

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

**Richt lijnen**: als u aangepaste Azure Policy definities of Azure Resource Manager sjablonen voor uw Azure-cache gebruikt voor redis-instanties en gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. cache ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. cache ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-cache voor redis-exemplaren en gerelateerde resources.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor virtuele machines van Azure of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure-cache voor redis-exemplaren, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om Azure-cache te vereenvoudigen en te beveiligen voor redis-geheim beheer. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

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

**Richt lijnen**: voor virtuele machines van Azure of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure-cache voor redis-exemplaren, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om Azure-cache te vereenvoudigen en te beveiligen voor redis-geheim beheer. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory. Met beheerde identiteiten kunt u zich verifiëren bij elke service die AAD-verificatie ondersteunt, met inbegrip van Azure Key Vault, zonder enige referenties in uw code.

Beheerde identiteiten configureren:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integratie met door Azure beheerde identiteiten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure cache voor redis), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure cache voor redis), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: redis-persistentie inschakelen. Met redis persistentie kunt u gegevens bewaren die zijn opgeslagen in redis. U kunt ook moment opnamen maken en back-ups maken van de gegevens, die u in het geval van een hardwarestoring kunt laden. Dit is een enorm voor deel van de Basic-of Standard-laag waarbij alle gegevens worden opgeslagen in het geheugen en er mogelijk gegevens verlies kan optreden in het geval van een storing waarbij cache knooppunten niet beschikbaar zijn.

U kunt ook Azure-cache gebruiken voor redis-export. Met exporteren kunt u de gegevens die zijn opgeslagen in azure cache exporteren voor redis naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van één Azure-cache te verplaatsen voor een redis-exemplaar naar een ander of een andere redis-server. Tijdens het export proces wordt er een tijdelijk bestand gemaakt op de virtuele machine die als host fungeert voor de Azure-cache voor het redis-server exemplaar, en wordt het bestand geüpload naar het aangewezen opslag account. Wanneer de export bewerking is voltooid met de status geslaagd of mislukt, wordt het tijdelijke bestand verwijderd.

Redis-persistentie inschakelen:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Azure cache gebruiken voor redis-export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: redis-persistentie inschakelen. Met redis persistentie kunt u gegevens bewaren die zijn opgeslagen in redis. U kunt ook moment opnamen maken en back-ups maken van de gegevens, die u in het geval van een hardwarestoring kunt laden. Dit is een enorm voor deel van de Basic-of Standard-laag waarbij alle gegevens worden opgeslagen in het geheugen en er mogelijk gegevens verlies kan optreden in het geval van een storing waarbij cache knooppunten niet beschikbaar zijn.

U kunt ook Azure-cache gebruiken voor redis-export. Met exporteren kunt u de gegevens die zijn opgeslagen in azure cache exporteren voor redis naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van één Azure-cache te verplaatsen voor een redis-exemplaar naar een ander of een andere redis-server. Tijdens het export proces wordt er een tijdelijk bestand gemaakt op de virtuele machine die als host fungeert voor de Azure-cache voor het redis-server exemplaar, en wordt het bestand geüpload naar het aangewezen opslag account. Wanneer de export bewerking is voltooid met de status geslaagd of mislukt, wordt het tijdelijke bestand verwijderd.

Als Azure Key Vault gebruiken om referenties voor uw Azure-cache op te slaan voor redis-instanties, moet u regel matig automatische back-ups van uw sleutels maken.

Redis-persistentie inschakelen:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Azure cache gebruiken voor redis-export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Back-ups maken van Key Vault sleutels:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Azure cache gebruiken voor het importeren van redis. Importeren kan worden gebruikt om redis-compatibele RDB-bestanden te halen van elke redis-server die in een wille keurige Cloud of omgeving wordt uitgevoerd, waaronder redis die wordt uitgevoerd op Linux, Windows of een Cloud provider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf gevulde gegevens te maken. Tijdens het import proces laadt Azure cache voor redis de RDB-bestanden vanuit Azure Storage in het geheugen en voegt vervolgens de sleutels in de cache in.

Test gegevens herstel van uw Azure Key Vault geheimen regel matig.

Azure cache gebruiken voor het importeren van redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Key Vault geheimen herstellen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Azure cache voor redis-back-ups van redis-export en redis-persistentie worden opgeslagen in uw geselecteerde Azure Storage-account. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling kan niet worden uitgeschakeld. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.

Informatie over versleuteling voor Azure Storage accounts: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: micro soft

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

Werk stroom automatisering configureren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Micro soft Security Response Center anatomie van een incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen reactie plan voor incidenten:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van de Red Teaming-en live site-indringings tests op door micro soft beheerde Cloud infrastructuur,-services en-toepassingen: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)