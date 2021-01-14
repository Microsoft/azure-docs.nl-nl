---
title: Azure-beveiligings basislijn voor Azure Container Registry
description: Azure-beveiligings basislijn voor Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 52cc67b1160ae8c9a0120a0a10d0131b167d5776
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202671"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure-beveiligings basislijn voor Azure Container Registry

De Azure-beveiligings basislijn voor Azure Container Registry bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: Azure Virtual Network biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Door de toegang tot uw persoonlijke Azure-container register te beperken vanuit een virtueel Azure-netwerk, zorgt u ervoor dat alleen bronnen in het virtuele netwerk toegang hebben tot het REGI ster. Voor cross-premises scenario's kunt u ook firewall regels configureren om alleen toegang tot het REGI ster vanuit specifieke IP-adressen toe te staan. Configureer vanaf een firewall firewall toegangs regels en service tags om toegang te krijgen tot uw container register.

Beperk de toegang tot een Azure container Registry met behulp van een virtueel netwerk of een firewall-regel van Azure: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Regels configureren voor toegang tot een Azure container Registry achter een firewall: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging om uw netwerk bronnen in azure te beveiligen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Beveilig uw netwerk bronnen: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor Azure App Service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Standard-beveiliging in voor uw virtuele netwerken voor beveiliging van DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.  Implementeer Azure Firewall op elke netwerk grenzen van de organisatie met behulp van bedreigings informatie en geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

U kunt Azure Security Center just-in-time-netwerk toegang gebruiken om Nsg's te configureren om de bloot stelling van eind punten te beperken tot goedgekeurde IP-adressen gedurende een beperkte periode. U kunt ook Azure Security Center adaptieve netwerk beveiliging gebruiken om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

DDoS-beveiliging configureren:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall implementeren: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Meer informatie over Azure Security Center adaptieve netwerk beveiliging: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center just-in-time-netwerk Access Control: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: Schakel de stroom logboeken voor netwerk beveiligings groepen (NSG) in voor de NSG die zijn gekoppeld aan het subnet dat wordt gebruikt voor het beveiligen van uw Azure container Registry. U kunt de NSG-stroom logboeken vastleggen in een Azure Storage-account om stroom records te genereren. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Azure Network Watcher pakket vastleggen in.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Selecteer een aanbieding op de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen en functies voor Payload-inspectie. Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren.

Azure Marketplace:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Azure Firewall implementeren: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwingen configureren met Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw container register, gebruikt u de tags voor de virtuele netwerk service voor de Azure container Registry-service om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetag naam ' AzureContainerRegistry ' op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Toegang toestaan per service label: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die zijn gekoppeld aan uw Azure-container registers met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. ContainerRegistry ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw container registers te controleren of af te dwingen. 

U kunt Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door het verpakken van sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk-definitie. De blauw druk Toep assen op nieuwe abonnementen en beheer en beheer op basis van versies.

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: klant kan gebruikmaken van Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en beleids regels, in één definitie van de blauw druk te brengen. De blauw druk Toep assen op nieuwe abonnementen en beheer en beheer op basis van versies.

Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw container registers. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt tijd bronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijd synchronisatie-instellingen voor uw reken resources te beheren.

Tijd synchronisatie voor Azure Compute-resources configureren: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door een Azure container Registry. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en een Analytics-account te gebruiken, en Azure Storage accounts voor lange termijn/archiverings opslag.

Azure Container Registry logboeken voor diagnostische evaluatie en controle:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure monitor verzamelt bron Logboeken (voorheen Diagnostische logboeken genoemd) voor door gebruikers gestuurde gebeurtenissen in het REGI ster. Verzamel en gebruik deze gegevens om register verificatie gebeurtenissen te controleren en een compleet activiteiten traject te geven op register artefacten, zoals pull-en push gebeurtenissen, zodat u beveiligings problemen met uw REGI ster kunt vaststellen.

Azure Container Registry logboeken voor diagnostische evaluatie en controle: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Azure container Registry logboeken analyseren en controleren op afwijkend gedrag en regel matig de resultaten bekijken. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens.

Azure Container Registry logboeken voor diagnostische evaluatie en controle:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Meer informatie over Log Analytics-werk ruimte: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: Azure log Analytics-werk ruimte gebruiken voor bewaking en waarschuwingen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen die betrekking hebben op uw Azure container Registry.

Azure Container Registry logboeken voor diagnostische evaluatie en controle: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Een waarschuwing over logboek gegevens van log Analytics:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing. Azure Container Registry geen aan anti-malware gerelateerde logboeken verwerkt of produceert.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing. Azure Container Registry is een eind punt en initieert geen communicatie en de service voert geen query uit op DNS.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Voor elk Azure container Registry moet u bijhouden of het ingebouwde beheerders account is in-of uitgeschakeld. Schakel het account uit wanneer het niet wordt gebruikt.

Een directory-rol verkrijgen in azure AD met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directory-rol in azure AD ophalen met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry-beheerders account:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory (Azure AD) beschikt niet over het concept van standaard wachtwoorden. Andere Azure-resources waarbij een wacht woord geforceerd moet worden gemaakt met behulp van complexiteits vereisten en een minimale wachtwoord lengte, wat afhankelijk is van de service. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen gebruiken.

Als het standaard beheerders account van een Azure container Registry is ingeschakeld, worden er automatisch complexe wacht woorden gemaakt en moeten ze worden gedraaid. Schakel het account uit wanneer het niet wordt gebruikt.

Azure Container Registry-beheerders account: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Maak ook procedures om het ingebouwde beheerders account van een container register in te scha kelen. Schakel het account uit wanneer het niet wordt gebruikt.

Meer informatie over Azure Security Center identiteit en toegang:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry-beheerders account:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory-SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

Gebruik voor individuele toegang tot het container register afzonderlijke aanmeldingen die zijn geïntegreerd met Azure Active Directory.

Meer informatie over eenmalige aanmelding met Azure AD:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Afzonderlijke aanmelding bij een container register:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

Meer informatie over privileged Access workstations:  https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center bewaking**: n.v.t.

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD)-beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

De identiteits-en toegangs activiteit van gebruikers controleren in Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Een Azure AD-exemplaar maken en configureren: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Meer informatie over Azure AD Reporting:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access revisies gebruiken:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Richt lijnen**: u hebt toegang tot Azure Active Directory (Azure AD) aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Security Information and Event Management (Siem)/monitoring-hulp programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

Azure-activiteiten logboeken integreren in Azure Monitor:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) risico-en identiteits beveiligings functies voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. 

Hoe kan ik Risk ante aanmeldingen voor Azure AD bekijken: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet beschikbaar; Klanten-lockbox wordt momenteel niet ondersteund voor Azure Container Registry.

Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: gebruik resource Tags om te helpen bij het volgen van Azure-container registers voor het opslaan of verwerken van gevoelige informatie.

Tags en versie container installatie kopieën of andere artefacten in een REGI ster, en vergren delen van installatie kopieën of opslag plaatsen, om te helpen bij het volgen van installatie kopieën die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Aanbevelingen voor het coderen en versie beheer van container installatie kopieën:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Een container installatie kopie in een Azure container Registry vergren delen:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke container registers, abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn.

Resources moeten worden gescheiden door een virtueel netwerk of subnet, worden op de juiste wijze gelabeld en beveiligd door een netwerk beveiligings groep (NSG) of Azure Firewall.

Aanvullende Azure-abonnementen maken:  https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheer groepen maken:  https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Beperk de toegang tot een Azure container Registry met behulp van een virtueel netwerk of een firewall-regel van Azure: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Een NSG maken met een beveiligings configuratie: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: Implementeer een geautomatiseerd hulp programma op netwerk verbindingen die worden bewaakt voor niet-geautoriseerde overdracht van gevoelige informatie en blokkeert deze overdrachten tijdens het melden van informatie over de beveiliging van uw mede werkers.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Zorg ervoor dat clients die verbinding maken met uw Azure container Registry kunnen onderhandelen over TLS 1,2 of hoger. Microsoft Azure resources standaard onderhandelen over TLS 1,2.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

Meer informatie over versleuteling in transit met Azure:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure container Registry. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot gegevens en resources in een Azure container Registry. 

Azure RBAC configureren:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Rollen en machtigingen Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: als dat nodig is voor de naleving van de reken bronnen, implementeert u een hulp programma van derden, zoals een geautomatiseerde oplossing voor het voor komen van gegevens verlies op basis van een host voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Gebruik versleuteling in rust voor alle Azure-resources. Standaard worden alle gegevens in een Azure container Registry op rest versleuteld met door micro soft beheerde sleutels.

Meer informatie over versleuteling in de rest van Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Door de klant beheerde sleutels in Azure Container Registry:  https://aka.ms/acr/cmk



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: Azure monitor verzamelt bron Logboeken (voorheen Diagnostische logboeken genoemd) voor door gebruikers gestuurde gebeurtenissen in het REGI ster. Verzamel en gebruik deze gegevens om register authenticatie gebeurtenissen te controleren en een compleet activiteiten traject te geven op register artefacten, zoals pull-en pull-gebeurtenissen, zodat u operationele problemen met uw REGI ster kunt vaststellen.

Azure Container Registry logboeken voor diagnostische evaluatie en controle: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg aanbevelingen van Azure Security Center over het uitvoeren van evaluatie van beveiligings problemen op uw container installatie kopieën. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace om evaluatie van beveiligings problemen in de installatie kopie uit te voeren.

Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry integratie met Security Center (preview-versie):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: micro soft voert patch beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure container Registry.

Automatisch bijwerken van container installatie kopieën wanneer updates van basis installatie kopieën van het besturings systeem en andere patches worden gedetecteerd.

Over updates van de basis installatie kopie voor Azure Container Registry taken:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: u kunt oplossingen van derden gebruiken om installatie kopieën van toepassingen te patchen.  U kunt ook Azure Container Registry taken uitvoeren om updates voor toepassings installatie kopieën in een container register te automatiseren op basis van beveiligings patches of andere updates in basis installatie kopieën.

Over updates van basis installatie kopieën voor ACR-taken:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: Integreer Azure container Registry (ACR) met Azure Security Center om het periodiek scannen van container installatie kopieën voor beveiligings problemen in te scha kelen. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace voor het uitvoeren van periodieke installatie kopieën van beveiligings problemen.

Azure Container Registry integratie met Security Center (preview-versie):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: Integreer Azure container Registry (ACR) met Azure Security Center om het periodiek scannen van container installatie kopieën in te scha kelen voor beveiligings problemen en om Risico's te classificeren. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace voor het uitvoeren van periodieke installatie kopieën en risico classificatie.

Azure Container Registry integratie met Security Center (preview-versie):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center bewaking**: n.v.t.

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Meer informatie over Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Azure container registry behoudt meta gegevens, inclusief tags en manifesten voor installatie kopieën in een REGI ster. Volg de aanbevolen procedures voor het labelen van artefacten.

Over registers, opslag plaatsen en installatie kopieën: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Aanbevelingen voor het coderen en versie beheer van container installatie kopieën: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Hulp**: Azure container registry behoudt meta gegevens, inclusief tags en manifesten voor installatie kopieën in een REGI ster. Volg de aanbevolen procedures voor het labelen van artefacten.

Over registers, opslag plaatsen en installatie kopieën: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Aanbevelingen voor het coderen en versie beheer van container installatie kopieën:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources maken conform uw organisatie behoeften.  

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy configureren en beheren:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: Azure container Registry logboeken analyseren en controleren op afwijkend gedrag en regel matig de resultaten bekijken. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens.

Azure Container Registry logboeken voor diagnostische evaluatie en controle:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Meer informatie over Log Analytics-werk ruimte:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources.  U kunt uw eigen oplossing implementeren voor het verwijderen van niet-geautoriseerde Azure-resources. Een inleiding tot Azure Automation:  https://docs.microsoft.com/azure/automation/automation-intro


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing. Bench Mark is ontworpen voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: Maak gebruik van Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing. Bench Mark is ontworpen voor reken resources.



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met kunt Manager via scripts beperken

**Richt lijnen**: gebruik specifieke configuraties van het besturings systeem of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.

Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Hulp**: gebruik specifieke configuraties van besturings systemen of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.

Bijvoorbeeld hoe u de uitvoering van Power shell-scripts kunt beheren in Windows-omgevingen:  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: software die vereist is voor bedrijfs activiteiten, maar die een groter risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure firewall of netwerk beveiligings groep.

Een virtueel netwerk maken:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy of Azure Security Center om beveiligings configuraties voor alle Azure-resources te onderhouden.

Azure Policy configureren en beheren:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: gebruik Azure Security Center aanbeveling ' Verhelp de beveiligings configuraties op uw virtual machines ' om beveiligings configuraties voor alle reken resources te onderhouden.

Azure Security Center aanbevelingen bewaken:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center aanbevelingen oplossen:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing. Bench Mark is van toepassing op reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik Azure Policy om systeem configuraties te signasen, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy configureren en beheren:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing. Bench Mark is van toepassing op reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans voor uw Azure-resources uit te voeren.

Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Aanbevelingen herstellen in Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Controleer de naleving van Azure-container registers met behulp van Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing. Bench Mark is van toepassing op reken resources.


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

Integratie met door Azure beheerde identiteiten:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

Gebruik een door Azure beheerde identiteit in Azure Container Registry taken:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten voor het leveren van Azure-Services met een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

Beheerde identiteiten configureren:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Een beheerde identiteit gebruiken om te verifiëren bij een Azure container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Referentie scanner instellen:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**: gebruik micro soft antimalware voor Azure Cloud Services en virtual machines om uw resources voortdurend te controleren en te beschermen. Gebruik voor Linux een oplossing van antimalware van derden.

Micro soft antimalware configureren voor Cloud Services en Virtual Machines:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure container Registry), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing. Bench Mark is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: de gegevens in uw Microsoft Azure container register worden altijd automatisch gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen. Azure Container Registry kopieert uw gegevens, zodat deze worden beveiligd tegen geplande en niet-geplande gebeurtenissen

Eventueel geo-replicatie van een container register voor het onderhouden van register replica's in meerdere Azure-regio's. 

Geo-replicatie in Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: Maak optioneel back-ups van container installatie kopieën door te importeren van het ene REGI ster naar het andere.

Maak een back-up van door de klant beheerde sleutels in Azure Key Vault met behulp van Azure-opdracht regel Programma's of Sdk's.

Container installatie kopieën importeren in een container register:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Hoe kan ik een back-up maken van sleutel kluis sleutels in Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: het herstellen van back-ups van door de klant beheerde sleutels in azure Key Vault met behulp van Azure-opdracht regel Programma's of sdk's testen.

Azure Key Vault sleutels herstellen in Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: u kunt Soft-Delete in azure Key Vault inschakelen om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Soft-Delete in Key Vault inschakelen: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

Werk stroom automatisering configureren in Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Micro soft Security Response Center anatomie van een incident:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen reactie plan voor incidenten:  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden:  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

De Azure Security Center Security-contact persoon instellen:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

Continue export configureren:  https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van de Red Teaming-en live site-indringings tests op door micro soft beheerde Cloud infrastructuur,-services en-toepassingen:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)
