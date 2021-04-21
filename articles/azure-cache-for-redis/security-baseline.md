---
title: Azure-beveiligingsbasislijn voor Azure Cache voor Redis
description: De Azure Cache voor Redis beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0397f53c54f488f6840e35212de3e51624f360d7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829994"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-beveiligingsbasislijn voor Azure Cache voor Redis

Met deze beveiligingsbasislijn worden richtlijnen van [azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Azure Cache voor Redis. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Cache voor Redis. **Besturingselementen** die niet van Azure Cache voor Redis zijn uitgesloten.

 
Zie het volledige Azure Cache voor Redis toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Cache voor Redis volledig is toegewezen aan de Azure [Azure Cache voor Redis Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Implementeer uw Azure Cache voor Redis-exemplaar binnen een virtueel netwerk (VNet). Een VNet is een particulier netwerk in de cloud. Wanneer een Azure Cache voor Redis is geconfigureerd met een VNet, is het niet openbaar adresseerbaar en is het alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.

U kunt ook firewallregels opgeven met een begin- en eind-IP-adresbereik. Wanneer firewallregels zijn geconfigureerd, kunnen alleen clientverbindingen uit de opgegeven IP-adresbereiken verbinding maken met de cache.

- [Ondersteuning voor Virtual Network Premium-Azure Cache voor Redis](cache-how-to-premium-vnet.md)

- [Firewallregels Azure Cache voor Redis configureren](./cache-configure.md#firewall)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en logboeken

**Richtlijnen:** wanneer Virtual Machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cache voor Redis-exemplaar, kunt u netwerkbeveiligingsgroepen (NSG's) gebruiken om het risico op gegevens exfiltratie te verminderen. Schakel NSG-stroomlogboeken in en verzend logboeken naar een Azure Storage-account voor verkeerscontrole. U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Implementatie van Azure Virtual Network (VNet) biedt verbeterde beveiliging en isolatie voor uw Azure Cache voor Redis, evenals subnetten, toegangscontrolebeleid en andere functies om de toegang verder te beperken. Wanneer een VNet wordt geïmplementeerd, Azure Cache voor Redis niet openbaar adresseerbaar en is deze alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.

Schakel DDoS Protection Standard in op de VNets die zijn gekoppeld aan uw Azure Cache voor Redis-exemplaren om u te beschermen tegen DDoS-aanvallen (Distributed Denial of Service). Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

- [Ondersteuning voor Virtual Network Premium-Azure Cache voor Redis](cache-how-to-premium-vnet.md)

- [Azure DDoS Protection Standard beheren met behulp van de Azure Portal](../ddos-protection/manage-ddos-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cache voor Redis-exemplaar, kunt u netwerkbeveiligingsgroepen (NSG's) gebruiken om het risico op gegevens exfiltratie te verminderen. Schakel NSG-stroomlogboeken in en verzend logboeken naar een Azure Storage-account voor verkeerscontrole. U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** wanneer u Azure Cache voor Redis gebruikt met uw webtoepassingen die worden uitgevoerd op Azure App Service- of reken-exemplaren, implementeert u alle resources in een Azure Virtual Network (VNet) en beveiligt u met een Azure Web Application Firewall (WAF) op Web Application Gateway. Configureer de WAF om te worden uitgevoerd in de preventiemodus. De preventiemodus blokkeert indringers en aanvallen die door de regels worden gedetecteerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

U kunt ook een aanbieding selecteren in de Azure Marketplace die ondersteuning biedt voor IDS/IPS-functionaliteit met mogelijkheden voor payloadinspectie en/of anomaliedetectie.

- [Inzicht in de mogelijkheden van Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** Servicetags voor virtuele netwerken gebruiken voor het definiëren van besturingselementen voor netwerktoegang in netwerkbeveiligingsgroepen (NSG's) of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres-voorvoegsels die door de servicetag worden omvat en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

U kunt ook toepassingsbeveiligingsgroepen (ASG) gebruiken om complexe beveiligingsconfiguratie te vereenvoudigen. Met ASG's kunt u netwerkbeveiliging configureren als een natuurlijke uitbreiding van de structuur van een toepassing, zodat u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van deze groepen.

- [Servicetags voor virtuele netwerken](../virtual-network/service-tags-overview.md)

- [Toepassingsbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen met betrekking tot uw Azure Cache voor Redis-exemplaren met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten Microsoft.Cache en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Cache voor Redis controleren of afdwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities, zoals:
- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

- De DDoS Protection-standaard moet zijn ingeschakeld

U kunt Azure Blueprints ook gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Manager-sjablonen, op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en beleidsregels, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen en omgevingen en beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** gebruik tags voor netwerkbronnen die zijn gekoppeld aan Azure Cache voor Redis implementatie om deze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Azure-activiteitenlogboek om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources die betrekking hebben op uw Azure Cache voor Redis exemplaren. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken in en verzend de logboeken naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure Cache voor Redis exemplaren op het niveau van het besturingsvlak. Met behulp van azure-activiteitenlogboekgegevens kunt u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure Cache voor Redis exemplaren.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken in en verzend de logboeken naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure Cache voor Redis exemplaren op het niveau van het besturingsvlak. Met behulp van azure-activiteitenlogboekgegevens kunt u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure Cache voor Redis exemplaren.

Hoewel metrische gegevens beschikbaar zijn door diagnostische instellingen in te stellen, is auditlogboekregistratie op het gegevensvlak nog niet beschikbaar voor Azure Cache voor Redis.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor logboekretentieperiode in voor Log Analytics-werkruimten die zijn gekoppeld aan uw Azure Cache voor Redis-instanties overeenkomstig de nalevingsvoorschriften van uw organisatie.

Houd er rekening mee dat auditlogregistratie op het gegevensvlak nog niet beschikbaar is voor Azure Cache voor Redis.

- [Retentieparameters voor logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Diagnostische instellingen voor Azure-activiteitenlogboeken inschakelen en de logboeken verzenden naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteitenlogboekgegevens die mogelijk zijn verzameld voor Azure Cache voor Redis.

Houd er rekening mee dat auditlogregistratie op het gegevensvlak nog niet beschikbaar is voor Azure Cache voor Redis.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Azure-activiteitenlogboeken verzamelen en analyseren in een Log Analytics-werkruimte in Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** u kunt configureren om waarschuwingen te ontvangen op basis van metrische gegevens en activiteitenlogboeken met betrekking tot uw Azure Cache voor Redis exemplaren. Azure Monitor kunt u een waarschuwing configureren om een e-mailmelding te verzenden, een webhook aan te roepen of een logische Azure-app aan te roepen.

Hoewel metrische gegevens beschikbaar zijn door diagnostische instellingen in te stellen, is auditlogboekregistratie op het gegevensvlak nog niet beschikbaar voor Azure Cache voor Redis.

- [Waarschuwingen configureren voor Azure Cache voor Redis](./cache-how-to-monitor.md#alerts)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en die kunnen worden opgevraagd. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Toegang tot de besturingsvlak Azure Cache voor Redis wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaardwachtwoorden.

Toegang tot de gegevensvlak Azure Cache voor Redis wordt beheerd via toegangssleutels. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache en kunnen op elk moment opnieuw worden ge regenereerd.

Het wordt afgeraden om standaardwachtwoorden in uw toepassing in te bouwen. In plaats daarvan kunt u uw wachtwoorden opslaan in Azure Key Vault en vervolgens Azure AD gebruiken om ze op te halen.

- [Toegangssleutels opnieuw Azure Cache voor Redis opnieuw](./cache-configure.md#settings)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwd Azure-beleid gebruiken om toegewezen beheerdersaccounts bij te houden, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Raadpleeg de volgende bronnen voor meer informatie:

- [Identiteit en toegang Azure Security Center bewaken (preview)](../security-center/security-center-identity-access.md)

- [Het gebruik van Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** Azure Cache voor Redis gebruikt toegangssleutels om gebruikers te verifiëren en biedt geen ondersteuning voor eenmalige aanmelding (SSO) op gegevensvlakniveau. Toegang tot het besturingsvlak voor Azure Cache voor Redis is beschikbaar via REST API en ondersteunt eenmalige aanmelding. Als u wilt verifiëren, stelt u de autorisatieheader voor uw aanvragen in op een JSON Web Token die u verkrijgt van Azure Active Directory (Azure AD).

- [Meer Azure Cache voor Redis REST API](/rest/api/redis/)

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Paw (Privileged Access Workstations) gebruiken met meervoudige verificatie geconfigureerd om u aan te melden bij En Azure-resources te configureren. 

- [Meer informatie over Privileged Access-werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

Gebruik bovendien Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

- [Een Privileged Identity Management (PIM) implementeren](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Inzicht in Azure AD-risicodetecties](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** Benoemde locaties configureren in Azure Active Directory (Azure AD) Voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

Azure AD-verificatie kan niet worden gebruikt voor directe toegang tot de gegevensvlak van Azure Cache voor Redis. Azure AD-referenties kunnen echter worden gebruikt voor beheer op het niveau van het besturingsvlak (dat wil zeggen de Azure Portal) voor het beheer van Azure Cache voor Redis-toegangssleutels.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om u te helpen verouderde accounts te vinden. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben.

- [Inzicht in Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Toegangsbeoordelingen voor Azure Identity gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt gewenste logboekwaarschuwingen configureren in Log Analytics.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Hoe kunt u de Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory(Azure AD) Identity Protection en risicodetectiefuncties voor accountaanmelding om geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten te configureren. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Azure Cache voor Redis moeten worden gescheiden door virtueel netwerk/subnet en op de juiste wijze worden getagd. U kunt eventueel de firewall Azure Cache voor Redis gebruiken om regels te definiëren, zodat alleen clientverbindingen uit opgegeven IP-adresbereiken verbinding kunnen maken met de cache.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Implementatie van Azure Cache voor Redis in een Vnet](cache-how-to-premium-vnet.md)

- [Firewallregels Azure Cache voor Redis configureren](./cache-configure.md#firewall)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** Nog niet beschikbaar; functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure Cache voor Redis.

Microsoft beheert de onderliggende infrastructuur voor Azure Cache voor Redis en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Azure Cache voor Redis vereist standaard met TLS versleutelde communicatie. TLS-versies 1.0, 1.1 en 1.2 worden momenteel ondersteund. TLS 1.0 en 1.1 zijn echter op weg naar afschaffing in de hele branche, dus gebruik indien mogelijk TLS 1.2. Als uw clientbibliotheek of hulpprogramma geen ondersteuning biedt voor TLS, kunt u niet-versleutelde verbindingen inschakelen via de api's voor Azure Portal of beheer. In dergelijke gevallen waarin versleutelde verbindingen niet mogelijk zijn, wordt het aanbevolen om uw cache en clienttoepassing in een virtueel netwerk te plaatsen.

- [Inzicht in versleuteling in transit voor Azure Cache voor Redis](cache-best-practices.md)

- [Inzicht in vereiste poorten die worden gebruikt in Vnet-cachescenario's](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Cache**:

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** functies voor gegevensidentificatie, classificatie en preventie van verlies zijn nog niet beschikbaar voor Azure Cache voor Redis. Tag exemplaren met gevoelige informatie als zodanig en implementeert een oplossing van derden als dit nodig is voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het veel te ver om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een reeks krachtige besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de toegang tot het Azure Cache voor Redis te bepalen (dat wil zeggen Azure Portal).

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Azure Cache voor Redis worden klantgegevens opgeslagen in het geheugen en hoewel deze sterk worden beveiligd door veel besturingselementen die door Microsoft worden geïmplementeerd, wordt het geheugen niet standaard versleuteld. Versleutel inhoud, indien vereist door uw organisatie, voordat u deze opslaat in Azure Cache voor Redis.

Als u de Azure Cache voor Redis functie 'Redis Data Persistence' gebruikt, worden gegevens verzonden naar een Azure Storage-account dat u bezit en beheert. U kunt persistentie configureren via de blade 'Nieuw Azure Cache voor Redis' tijdens het maken van de cache en in het menu Resource voor bestaande Premium-caches.

Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met behulp van 256-bits AES-versleuteling. Dit is een van de sterkste blokcoderingscodes die compatibel is met FIPS 140-2. Azure Storage-versleuteling kan niet worden uitgeschakeld. U kunt vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw opslagaccount of u kunt versleuteling beheren met uw eigen sleutels.

- [Persistentie configureren in Azure Cache voor Redis](cache-how-to-premium-persistence.md)

- [Versleuteling voor Azure Storage accounts](../storage/common/storage-service-encryption.md)

- [Inzicht krijgen in gegevensbeveiliging van Azure-klanten](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer wijzigingen plaatsvinden in productie-exemplaren van Azure Cache voor Redis en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van beveiligingsleed uitvoeren

**Richtlijnen:** volg de aanbevelingen van Azure Security Center over het beveiligen Azure Cache voor Redis exemplaren en gerelateerde resources.

Microsoft voert vulnerability management uit op de onderliggende systemen die ondersteuning bieden Azure Cache voor Redis.

- [Meer Azure Security Center aanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen/ontdekken van alle resources (zoals compute, opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement(en). Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources die metagegevens geven om ze logisch te organiseren in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om uw Azure Cache voor Redis en gerelateerde resources te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

Gebruik daarnaast Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Resourcetags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik daarnaast Azure Resource Graph om resources in de abonnementen op te vragen en te ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Configureer voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Cache voor Redis-exemplaren met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.Cache om aangepaste beleidsregels te maken om de configuratie van uw Azure Cache voor Redis te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op uw Azure Cache voor Redis s, zoals:

- Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld

Raadpleeg de volgende bronnen voor meer informatie:

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy-definities of Azure Resource Manager-sjablonen gebruikt voor uw Azure Cache voor Redis-exemplaren en gerelateerde resources, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure-repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy-aliassen in de naamruimte Microsoft.Cache om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy-aliassen in de naamruimte Microsoft.Cache om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Gebruik Azure Policy [audit], [deny] en [deploy if not exist] om automatisch configuraties af te dwingen voor uw Azure Cache voor Redis en gerelateerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure App Service die worden gebruikt voor toegang tot uw Azure Cache voor Redis-exemplaren, gebruikt u Managed Service Identity in combinatie met Azure Key Vault om het beheer van Azure Cache voor Redis-geheimen te vereenvoudigen Azure Cache voor Redis beveiligen. Zorg Key Vault verwijderen is ingeschakeld.

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault](../key-vault/general/quick-create-portal.md)

- [Verificatie bij Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure App Service die worden gebruikt voor toegang tot uw Azure Cache voor Redis-exemplaren, gebruikt u Managed Service Identity in combinatie met Azure Key Vault om het beheer van Azure Cache voor Redis-geheimen te vereenvoudigen Azure Cache voor Redis beveiligen. Zorg Key Vault de functie Voor verwijderen is ingeschakeld.

Gebruik Beheerde identiteiten om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Azure Key Vault zonder referenties in uw code.

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden die moeten worden geüpload naar niet-compute Azure-resources vooraf scannen

**Richtlijnen:** Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Cache voor Redis), maar wordt niet uitgevoerd op klantinhoud.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-resources, zoals App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, enzovoort. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Redis-persistentie inschakelen. Met Redis-persistentie kunt u gegevens persistent maken die zijn opgeslagen in Redis. U kunt ook momentopnamen maken en een back-up maken van de gegevens, die u kunt laden in het geval van een hardwarestoring. Dit is een groot voordeel ten opzichte van de Basic- of Standard-laag, waarbij alle gegevens in het geheugen worden opgeslagen en er mogelijk gegevensverlies kan zijn in het geval van een storing waarbij cacheknooppunten niet beschikbaar zijn.

U kunt ook Azure Cache voor Redis Exporteren. Met Exporteren kunt u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van het ene exemplaar Azure Cache voor Redis naar een andere of een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de virtuele machine die als host voor het Azure Cache voor Redis-serverexe geval wordt gebruikt en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met de status Geslaagd of Mislukt, wordt het tijdelijke bestand verwijderd.

- [Redis-persistentie inschakelen](cache-how-to-premium-persistence.md)

- [Het gebruik van Azure Cache voor Redis Exporteren](cache-how-to-import-export-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Redis-persistentie inschakelen. Met Redis-persistentie kunt u gegevens persistent maken die zijn opgeslagen in Redis. U kunt ook momentopnamen maken en een back-up maken van de gegevens, die u kunt laden in het geval van een hardwarestoring. Dit is een groot voordeel ten opzichte van de Basic- of Standard-laag, waarbij alle gegevens in het geheugen worden opgeslagen en er mogelijk gegevensverlies kan zijn in het geval van een fout waarbij cacheknooppunten niet beschikbaar zijn.

U kunt ook Azure Cache voor Redis Exporteren. Met Exporteren kunt u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van het ene exemplaar Azure Cache voor Redis naar een andere of een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de virtuele machine die als host voor het Azure Cache voor Redis-serverexe geval wordt gebruikt en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met de status Geslaagd of Mislukt, wordt het tijdelijke bestand verwijderd.

Als u Azure Key Vault gebruikt om referenties voor uw Azure Cache voor Redis op te slaan, moet u ervoor zorgen dat uw sleutels regelmatig automatisch worden back-ups gemaakt.

- [Redis-persistentie inschakelen](cache-how-to-premium-persistence.md)

- [Het gebruik van Azure Cache voor Redis Exporteren](cache-how-to-import-export-data.md)

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Gebruik Azure Cache voor Redis importeren. Importeren kan worden gebruikt om Redis-compatibele RDB-bestanden te importeren vanaf elke Redis-server die wordt uitgevoerd in elke cloud of omgeving, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en andere. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf ingevulde gegevens te maken. Tijdens het importeren worden Azure Cache voor Redis RDB-bestanden uit Azure Storage in het geheugen geladen en vervolgens in de cache geplaatst.

Test periodiek het herstel van gegevens van uw Azure Key Vault geheimen.

- [Het gebruik van Azure Cache voor Redis importeren](cache-how-to-import-export-data.md)

- [Uw geheimen Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe te wijzen om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit is die heeft geleid tot de waarschuwing.

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) en een naamgevingssysteem maken om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor het testen, trainen en trainen van programma's voor IT-abonnementen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
