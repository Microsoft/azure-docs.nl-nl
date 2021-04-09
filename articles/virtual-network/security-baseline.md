---
title: Azure-beveiligings basislijn voor Virtual Network
description: De Virtual Network Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666570"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Azure-beveiligings basislijn voor Virtual Network

Deze beveiligings basislijn is van toepassing op richt lijnen van de [Azure Security Bench Mark versie 1,0](../security/benchmarks/overview-v1.md) naar Azure Virtual Network. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Virtual Network. **Besturings elementen** die niet van toepassing zijn op Azure Virtual Network, zijn uitgesloten.

Zie het [volledige azure Virtual Network Security Baseline-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over de manier waarop Azure Virtual Network volledig is toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Security Center en volg aanbevelingen voor netwerk beveiliging om uw netwerk bronnen in azure te beveiligen. 

Verzend logboeken stroom voor netwerk beveiligings groepen naar een Log Analytics-werk ruimte en gebruik Traffic Analytics om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Traffic Analytics biedt de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren. 

Gebruik Azure Monitor Logboeken om inzicht te krijgen in uw omgeving. Een werk ruimte moet worden gebruikt voor het sorteren en analyseren van de gegevens en kan worden geïntegreerd met andere Azure-Services, zoals Application Insights en Security Center. 

Kies bron logboeken voor verzen ding naar een Azure-opslag account of een Event Hub. U kunt ook een ander platform gebruiken om de logboeken te analyseren. 

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Security Center](../security-center/security-center-network-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS-standaard beveiliging (Distributed Denial of service) in op uw Azure-Virtual Network om te beschermen tegen DDoS-aanvallen.

Implementeer Azure Firewall op alle netwerk grenzen van de organisatie met op bedreigingen gebaseerd filteren en is geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

Gebruik de functies van beveiliging tegen bedreigingen van Security Center om communicatie met bekende schadelijke IP-adressen te detecteren.

Pas de aanbevelingen voor adaptieve netwerk beveiliging van Security Center toe voor configuraties van netwerk beveiligings groepen die poort-en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie. 

- [Azure DDoS Protection Standard beheren met de Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall op bedreigingen gebaseerd filteren](../firewall/threat-intel.md)

- [Beveiliging tegen bedreigingen in Security Center](../security-center/azure-defender.md)

- [Adaptieve netwerk beveiliging in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: gebruik de pakket opname van VPN gateway naast de meest beschik bare hulpprogram ma's voor het vastleggen van pakketten om netwerk pakketten vast te leggen. 

U kunt ook oplossingen op basis van agents of NVA bekijken die Terminal Access Point (tikken) of netwerk zichtbaarheids functionaliteit bieden via pakket Broker-partner oplossingen die beschikbaar zijn in azure Marketplace-aanbiedingen.

- [Pakket opnames voor VPN-gateways configureren](../vpn-gateway/packet-capture.md) 

- [Virtueel netwerk apparaat-partner](https://azure.microsoft.com/solutions/network-appliances)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: gebruik een Azure firewall dat is geïmplementeerd in uw virtuele netwerk en waarop Threat Intelligence is ingeschakeld. Gebruik Azure Firewall op bedreigingen gebaseerd filteren om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering of te weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. 

U kunt ook een geschikte aanbieding uit de Azure Marketplace selecteren die ondersteuning biedt voor ID'S/IP-adressen en functies voor nettolading inspectie.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren.

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Hiermee staat u het verkeer voor de bijbehorende service toe of weigert u door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Gebruik toepassings beveiligings groepen om complexe beveiligings configuratie te vereenvoudigen. Met toepassings beveiligings groepen kunt u netwerk beveiliging configureren als een natuurlijke uitbrei ding van de structuur van een toepassing. Zo kunt u virtuele machines groeperen en netwerk beveiligings beleid definiëren op basis van die groepen.

- [Service Tags begrijpen en gebruiken](service-tags-overview.md)

- [Toepassings beveiligings groepen begrijpen en gebruiken](./network-security-groups-overview.md#application-security-groups)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor netwerk bronnen definiëren en implementeren met Azure Policy en de ingebouwde netwerk beleids definities voor implementatie controleren.

Raadpleeg het standaard beleid voor Security Center dat beschik bare beveiligings aanbevelingen bevat die betrekking hebben op uw virtuele netwerken.

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-toewijzingen (op rollen gebaseerd toegangs beheer) en-beleid, in een enkele blauw definitie te verpakken. Azure Blueprint kunnen worden toegepast op nieuwe abonnementen voor nauw keurig afgestemde controle en beheer via versie beheer. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](../governance/policy/samples/built-in-policies.md#network) 

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

- [Bewaking inschakelen in Azure Security Center](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik het veld Beschrijving om de bedrijfs behoeften, duur en andere informatie op te geven voor regels die verkeer naar of van een netwerk voor afzonderlijke regels van een netwerk beveiligings groep toestaan.
Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

Kies Azure PowerShell of Azure CLI om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen in uw virtuele netwerk op te sporen. Maak waarschuwingen binnen Azure Monitor die worden geactiveerd wanneer er wijzigingen in essentiële bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Azure monitor in voor toegang tot uw audit-en activiteiten logboeken, waaronder gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. 

Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn/archiverings opslag.
U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Azure monitor in voor toegang tot uw audit-en activiteiten logboeken, waaronder gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn opslag/archivering van Bewaar periode van het beveiligings logboek.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig resultaten bekijken. Gebruik de Log Analytics-werk ruimte van Azure Monitor om een query uit te voeren op analyses en deze te gebruiken, en gebruik Azure Storage-accounts voor lange termijn/archiverings opslag. 

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Log Analytics-werk ruimte begrijpen](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Security Center met log Analytics werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden.

U kunt ook gegevens voor een Azure-Sentinel of een SIEM van derden inschakelen en vrijgeven voor waarschuwingen.

- [Waarschuwingen beheren in Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie van oplossingen conform uw organisatie behoeften.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: gebruik Azure Active Directory (Azure AD) ingebouwde beheerders rollen die expliciet kunnen worden toegewezen en die query's kan uitvoeren. 

Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik het identiteits-en toegangs beheer van Security Center om het aantal beheerders accounts te bewaken.

Schakel just-in-time/alleen-voldoende toegang in met behulp van Azure AD Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure Resource Manager. 

- [Meer informatie over Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Hulp**: gebruik SSO met Azure Active Directory (Azure AD) in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik de aanbevelingen voor identiteits-en toegangs beheer van Security Center.

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication (MFA) in en volg de aanbevelingen voor identiteits-en toegangs beheer van Security Center.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication (MFA) die zijn geconfigureerd om u aan te melden bij en toegang te krijgen tot Azure-netwerk bronnen.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. 

Security Center waarschuwingen voor risico detectie opnemen in Azure Monitor en aangepaste waarschuwingen/meldingen configureren met actie groepen.

- [Meer informatie over Security Center risico detecties (verdachte activiteiten)](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](../azure-monitor/alerts/action-groups.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem voor uw services. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties.  

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory (Azure AD) om logboeken te maken waarmee u verlopen accounts kunt detecteren. 

Azure Identity Access revisies kan worden uitgevoerd om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang moet regel matig worden gecontroleerd om ervoor te zorgen dat alleen de actieve gebruikers de toegang blijven hebben.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Integreer Azure Active Directory (Azure AD)-aanmeldings activiteiten, controle en risico gebeurtenis logboek bronnen, met elk Siem of bewakings programma op basis van uw toegang. 

Stroom lijn dit proces door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. Alle gewenste waarschuwingen kunnen worden geconfigureerd in Log Analytics werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik de functies van risico-en identiteits beveiliging van Azure Active Directory (Azure AD) voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten voor uw virtuele netwerk. Gegevens opnemen in azure Sentinel voor verdere onderzoeken.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources in uw virtuele netwerken kunnen onderhandelen over TLS 1,2 of hoger. Volg Security Center aanbevelingen voor versleuteling bij rest en versleuteling tijdens de overdracht. 

Micro soft biedt verschillende opties die door klanten kunnen worden gebruikt voor het intern beveiligen van gegevens binnen het Azure-netwerk en extern via internet naar de eind gebruiker. Hieronder vallen communicatie via virtuele particuliere netwerken (waarbij IPsec/IKE-versleuteling wordt gebruikt), Transport Layer Security (TLS) 1,2 of hoger (via Azure-onderdelen, zoals Application Gateway of Azure front deur), de protocollen rechtstreeks op de virtuele machines van Azure (zoals Windows IPsec of SMB), en meer.

Daarnaast is "versleuteling standaard" met behulp van MACsec (een IEEE-standaard op de Data Link-laag) ingeschakeld voor alle Azure-verkeer tussen Azure-Data Centers om de vertrouwelijkheid en integriteit van klant gegevens te waarborgen.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot gegevens en resources te beheren. U kunt ook servicespecifieke toegangs beheer methoden gebruiken. 

Kies ingebouwde rollen zoals eigenaar, bijdrager of netwerk bijdrager en wijs de rol toe aan het juiste bereik. U kunt bijvoorbeeld een subset van de mogelijkheden van het virtuele netwerk toewijzen met de specifieke machtigingen die zijn vereist voor virtuele netwerken voor een van deze rollen. 

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Virtuele netwerken plannen](virtual-network-vnet-plan-design-arm.md#permissions)

- [De ingebouwde rollen van Azure bekijken](../role-based-access-control/built-in-roles.md#networking)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met de activiteiten logboeken van Azure om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële Azure-resources, zoals virtuele netwerken en netwerk beveiligings groepen.

- [Diagnostische logboek registratie voor een netwerk beveiligings groep](virtual-network-nsg-manage-log.md)

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle netwerk bronnen, zoals virtuele netwerken, subnetten in uw abonnementen, op te vragen en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md) 

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om het virtuele netwerk en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md) 

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md) 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources maken conform uw organisatie behoeften.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen 

- Toegestane brontypen 

Zoek of Analyseer resources binnen de abonnementen met Azure resource Graph in omgevingen met een hoog beveiligings niveau, zoals die met Azure Storage accounts. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: voor komen dat resources worden gemaakt of gebruikt met Azure policy zoals vereist door het beleid van de organisatie. Implementeer processen voor het verwijderen van niet-geautoriseerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen 

- Toegestane brontypen 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om de interactie van de gebruiker met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure-netwerk resources te controleren of af te dwingen en ook ingebouwde Azure Policy definities te gebruiken.

Exporteer een van uw bouw sjablonen met Azure Resource Manager in JavaScript Object Notation (JSON) form en controleer deze om te controleren of de configuraties voldoen aan of de beveiligings vereisten voor uw organisatie overschrijden.

Implementeer aanbevelingen van Security Center als een veilige configuratie basislijn voor uw Azure-resources.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Resource Manager sjablonen en Azure-beleid om Azure-resources die zijn gekoppeld aan het virtuele netwerk en gerelateerde resources, veilig te configureren.  Azure Resource Manager sjablonen zijn JSON-bestanden (JavaScript Object Notation) die worden gebruikt voor het implementeren van virtuele machines samen met Azure-resources. Micro soft voert het onderhoud uit op basis van deze sjablonen.  

Gebruik Azure Policy [deny] en [implementatie indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure-resources.

- [Informatie over het maken van Azure Resource Manager sjablonen](../virtual-machines/windows/ps-template.md) 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Wat zijn Azure Policy effecten?](../governance/policy/concepts/effects.md)

- [Voorbeeldsjablonen van Azure Resource Manager voor virtueel netwerk](template-samples.md)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen, desired state Configuration-scripts. enzovoort.

U moet over machtigingen beschikken voor toegang tot de resources die u wilt beheren in azure DevOps, zoals uw code, builds en werk bijhouden. De meeste machtigingen worden verleend via ingebouwde beveiligings groepen. U kunt machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met Team Foundation Server.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure-resources definiëren en implementeren met behulp van Azure Policy. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van uw Azure-resources en alle ingebouwde beleids definities met betrekking tot specifieke bronnen. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Security Center om basislijn scans uit te voeren voor uw Azure Virtual Network en gerelateerde resources. Gebruik Azure Policy om een waarschuwing te krijgen en Azure-resource configuraties te controleren.

- [Aanbevelingen herstellen in Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om geheim beheer te vereenvoudigen en beveiligen voor uw Azure-resources die worden gehost in een azure-Virtual Network.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md) 

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: gebruik Azure Resource Manager om een virtueel netwerk en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die kunnen worden gebruikt als back-ups om virtueel netwerk en gerelateerde resources te herstellen.  Gebruik Azure Automation om de API van de Azure Resource Manager-sjabloon regel matig te kunnen aanroepen.

- [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Voorbeeldsjablonen van Azure Resource Manager voor virtueel netwerk](template-samples.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resource groepen-sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

- [Inleiding tot Azure Automation](../automation/automation-intro.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik Azure Resource Manager om een virtueel netwerk en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die kunnen worden gebruikt als back-ups om virtueel netwerk en gerelateerde resources te herstellen. Gebruik Azure Automation om de API van de Azure Resource Manager-sjabloon regel matig te kunnen aanroepen. Maak een back-up van door de klant beheerde sleutels binnen Azure Key Vault.

- [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Voorbeeldsjablonen van Azure Resource Manager voor virtueel netwerk](template-samples.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resource groepen-sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

- [Inleiding tot Azure Automation](../automation/automation-intro.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: regel matig de implementatie van Azure Resource Manager sjablonen uitvoeren op een geïsoleerd abonnement en het herstellen van back-ups van door de klant beheerde sleutels testen.

- [Resources implementeren met ARM-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities en Azure Resource Manager sjablonen. 

Machtigingen verlenen of weigeren voor specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met Team Foundation Server.  

Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor de beveiliging van door de klant beheerde sleutels.   

Schakel Soft-Delete in en verwijder de beveiliging in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Soft-Delete inschakelen en beveiliging opschonen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.  

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Markeer abonnementen (bijvoorbeeld productie of niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligings waarschuwingen in Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. 

U kunt ook de Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)