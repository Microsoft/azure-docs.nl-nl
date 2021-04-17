---
title: Azure-beveiligingsbasislijn voor Azure Cognitive Search
description: De Azure Cognitive Search beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589190"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure-beveiligingsbasislijn voor Azure Cognitive Search

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0 toe](../security/benchmarks/overview-v1.md) op Azure Cognitive Search. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Cognitive Search. **Besturingselementen** die niet van Azure Cognitive Search of de klant zijn uitgesloten.

Zie het volledige Azure Cognitive Search toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Cognitive Search volledig is toegewezen aan de Azure Security [Azure Cognitive Search-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Zorg ervoor Microsoft Azure Virtual Network voor alle subnetimplementaties een netwerkbeveiligingsgroep is toegepast met regels voor het implementeren van een toegangsschema met de minste bevoegdheden. Sta alleen toegang tot de vertrouwde poorten en IP-adresbereiken van uw toepassing toe. Implementeer Azure Cognitive Search een Azure-privé-eindpunt, indien mogelijk, om privétoegang tot uw services vanuit uw virtuele netwerk mogelijk te maken.

Cognitive Search ondersteunt ook aanvullende netwerkbeveiligingsfunctionaliteit voor het beheren van netwerktoegangsbeheerlijsten. Configureer uw zoekservice zodanig dat alleen communicatie met vertrouwde bronnen wordt toegestaan door de toegang van specifieke openbare IP-adresbereiken te beperken met behulp van de firewallmogelijkheden.

- [Privé-eindpunten configureren voor Azure Cognitive Search](service-create-private-endpoint.md)

- [De firewall voor Azure Cognitive Search configureren](service-configure-firewall.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's bewaken en in een logboek plaatsen

**Richtlijnen:** Cognitive Search kunnen niet rechtstreeks in een virtueel netwerk worden geïmplementeerd. Als uw clienttoepassing of gegevensbronnen zich echter in een virtueel netwerk, kunt u verkeer voor die onderdelen in het netwerk bewaken en in een logboek opslaan, inclusief aanvragen die naar een zoekservice in de cloud worden verzonden. Standaardaanbevelingen omvatten het inschakelen van een stroomlogboek voor een netwerkbeveiligingsgroep en het verzenden van logboeken naar Azure Storage of een Log Analytics-werkruimte. U kunt eventueel een Traffic Analytics om inzicht te krijgen in verkeerspatronen.

- [Stroomlogboeken van netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geboden door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Cognitive Search biedt geen specifieke functie voor het bestrijden van een gedistribueerde Denial of Service-aanval, maar u kunt DDoS Protection Standard inschakelen op de virtuele netwerken die zijn gekoppeld aan uw Cognitive Search-service voor algemene beveiliging.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** Schakel stroomlogboeken voor netwerkbeveiligingsgroepen in voor de netwerkbeveiligingsgroepen die Azure Virtual Machines (VM) beveiligen en die verbinding maken met uw Cognitive Search service. Logboeken verzenden naar een Azure Storage voor verkeerscontrole. 

Schakel Network Watcher pakketopname in als dat nodig is voor het onderzoeken van afwijkende activiteiten.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Cognitive Search biedt geen ondersteuning voor netwerkindringingsdetectie, maar als beperking van binnendringing kunt u firewallregels configureren om de IP-adressen op te geven die door de Cognitive Search service worden geaccepteerd. Configureer een privé-eindpunt om zoekverkeer weg te houden van het openbare internet.

- [Door de klant beheerde sleutels configureren voor gegevensversleuteling](search-security-manage-encryption-keys.md)

- [Door de klant beheerde sleutelinformatie verkrijgen uit indexen en synoniemenkaarten](search-security-get-encryption-keys.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik servicetags als u gebruik maakt van indexen en vaardighedensets in Cognitive Search, om een bereik van IP-adressen weer te geven die zijn machtigingen om verbinding te maken met externe resources. 

Sta verkeer naar resources toe of weiger dit door de naam van de servicetag (bijvoorbeeld AzureCognitiveSearch) op te geven in het juiste bron- of doelveld van een regel. 

- [Servicetags voor virtuele netwerken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** u kunt een Cognitive Search een privé-eindpunt van Azure configureren om uw zoekservice te integreren met een virtueel netwerk.  Gebruik resourcetags voor netwerkbeveiligingsgroepen en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Gebruik voor afzonderlijke regels voor netwerkbeveiligingsgroep het veld Beschrijving om de regels te documenteren die verkeer van/naar een netwerk toestaan. 
 

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals 'Tag vereisen en de waarde ervan'-effecten, om ervoor te zorgen dat alle resources worden gemaakt met tags en om u op de hoogte te stellen van bestaande resources zonder tag. 

U kunt Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.  

 
- [Een privé-eindpunt maken voor Cognitive Search](service-create-private-endpoint.md) 

 
 
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Azure-Virtual Network](../virtual-network/quick-create-portal.md) 

 
- [Netwerkverkeer filteren met regels voor netwerkbeveiligingsgroep](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen die betrekking hebben op Cognitive Search via Azure Monitor voor het samenvoegen van beveiligingsgegevens die zijn gegenereerd door eindpuntapparaten, netwerkbronnen en andere beveiligingssystemen. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren en gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag. U kunt deze gegevens ook inschakelen en inschakelen voor Azure Sentinel siem van derden.
 

 
- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Diagnostische en operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Cognitive Search en zijn handig voor het bewaken van de service en voor workloads die toegang hebben tot uw service.  Als u diagnostische gegevens wilt vastleggen, kunt u logboekregistratie inschakelen door op te geven waar logboekregistratiegegevens worden opgeslagen.
 

 
- [Logboekgegevens verzamelen en analyseren voor Azure Cognitive Search](search-monitor-logs.md) 

 
- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Search:**

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** historische gegevens die worden gefeed in diagnostische metrische gegevens, worden standaard 30 Cognitive Search bewaard. Voor een langere retentie moet u de instelling inschakelen waarmee een opslagoptie wordt opgegeven voor het persistent maken van vastgelegde gebeurtenissen en metrische gegevens.
 

 
Stel Azure Monitor log analytics-werkruimte in op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag. 
 

 
- [De gegevensretentieperiode in Log Analytics wijzigen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Retentiebeleid configureren voor Azure Storage accountlogboeken](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Logboeken van uw Cognitive Search service op afwijkende gedragingen analyseren en bewaken. Gebruik Azure Monitor Log Analytics om logboeken te controleren en query's uit te voeren op logboekgegevens. U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden. 

 
 
- [Logboekgegevens verzamelen en analyseren voor Cognitive Search](search-monitor-logs.md)
 
- [Zoeklogboekgegevens visualiseren in Power BI](search-monitor-logs-powerbi.md)
 

 
- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Meer informatie over Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Security Center Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en -gebeurtenissen. U kunt ook gegevens inschakelen en inschakelen om gegevens in te Azure Sentinel.
 

 
- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Waarschuwingen voor logboekgegevens van Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, service-principals en beheerde identiteiten groepeert. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal.

Cognitive Search zijn gekoppeld aan machtigingen die ondersteuning bieden voor beheertaken op serviceniveau. Deze rollen verlenen geen toegang tot het service-eindpunt. Toegang tot bewerkingen op het eindpunt (zoals indexbeheer, indexpopulatie en query's op zoekgegevens), gebruikt API-sleutels om de aanvraag te verifiëren.

- [Rollen instellen voor beheerderstoegang tot Azure Cognitive Search](search-security-rbac.md)

- [API-sleutels maken en beheren voor een Azure Cognitive Search service](search-security-api-keys.md)

- [Een directoryrol krijgen in Azure Active Directory (Azure AD) met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Cognitive Search heeft niet het concept van beheerdersaccounts op lokaal niveau of Azure Active Directory (Azure AD) die kunnen worden gebruikt voor het beheren van indexen en bewerkingen. 

Gebruik de ingebouwde Azure AD-rollen die expliciet moeten worden toegewezen voor beheerbewerkingen. Roep de Azure AD PowerShell-module aan om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Rollen gebruiken voor beheerderstoegang in Cognitive Search](search-security-rbac.md)

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Eenmalige aanmelding (SSO) gebruiken met Azure Active Directory

**Richtlijnen:** Gebruik SSO-verificatie met Azure Active Directory (Azure AD) voor toegang tot zoekservicegegevens voor beheerbewerkingen die worden ondersteund via Azure Resource Manager. 

Stel een proces in om het aantal identiteiten en referenties te verminderen door eenmalige aanmelding in te stellen voor de service met de bestaande identiteiten van uw organisatie.

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** schakel Azure Active Directory meervoudige verificatiefunctie (Azure AD) in en volg Security Center aanbevelingen voor identiteit en toegang.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Gebruik een PAW (Privileged Access Workstation) met meervoudige verificatie geconfigureerd om u aan te melden bij en toegang te krijgen tot Azure-resources.
 

 
- [Inzicht krijgen in beveiligde, door Azure beheerde werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Meervoudige verificatie Azure Active Directory (Azure AD) inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten en -bewaking om te detecteren wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor beheertaken op serviceniveau in Azure Cognitive Search. Azure AD-identiteiten verlenen geen toegang tot het eindpunt van de zoekservice.  Toegang tot bewerkingen zoals indexbeheer, indexpopulatie en query's op zoekgegevens zijn beschikbaar via API-sleutels.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [API-sleutels maken en beheren voor een Azure Cognitive Search service](search-security-api-keys.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik identiteits- en toegangsbeoordelingen van Azure AD om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Bekijk diagnostische logboeken van Cognitive Search voor activiteit in het eindpunt van de zoekservice, zoals indexbeheer, indexpopulatie en query's.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Bewerkingen en activiteit van de Azure Cognitive Search](search-monitor-usage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Toegang tot Azure Active Directory bronnen voor aanmeldingsactiviteiten, controles en risicogebeurtenislogboekbronnen (Azure AD) kunt u integreren met elk SIEM- of bewakingshulpprogramma.

Stroomlijn dit proces door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. Configureer de gewenste waarschuwingen in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren met Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection-functies om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Gegevens opnemen in Azure Sentinel voor verder onderzoek, indien nodig.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Resources moeten worden gescheiden door een virtueel netwerk/subnet, op de juiste wijze worden getagd en beveiligd binnen een netwerkbeveiligingsgroep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Gebruik Private Link om een privé-eindpunt te configureren voor Cognitive Search.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een privé-eindpunt maken voor Cognitive Search](service-create-private-endpoint.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** Gebruik een oplossing van derden van Azure Marketplace in netwerkperimeters om te controleren op onbevoegde overdracht van gevoelige informatie en om dergelijke overdrachten te blokkeren tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Microsoft beheert het onderliggende platform en behandelt alle klantinhoud als gevoelig en beschermt tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een reeks krachtige besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** functies voor gegevensidentificatie, classificatie en preventie van verlies zijn nog niet beschikbaar voor Cognitive Search. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden. 

Microsoft beheert het onderliggende platform en behandelt alle klantinhoud als gevoelig en beschermt tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om toegang tot resources te beheren

**Richtlijnen:** voor servicebeheer gebruikt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de toegang tot sleutels en configuratie te beheren. Voor inhoudsbewerkingen, zoals indexeren en query's, gebruikt Cognitive Search sleutels in plaats van een op identiteit gebaseerd model voor toegangsbeheer. Gebruik Azure RBAC om de toegang tot sleutels te bepalen. 

 
- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Rollen gebruiken voor beheerderstoegang tot Cognitive Search](search-security-rbac.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Cognitive Search geïndexeerde inhoud at rest automatisch versleuteld met door Microsoft beheerde sleutels. Als u meer beveiliging nodig hebt, kunt u standaardversleuteling aanvullen met een tweede versleutelingslaag met behulp van sleutels die u maakt en beheert in Azure Key Vault.

- [Door de klant beheerde sleutels configureren voor gegevensversleuteling in Azure Cognitive Search](search-security-manage-encryption-keys.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in productie-exemplaren van Cognitive Search en andere kritieke of gerelateerde resources. 

 
- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Waarschuwingen maken voor Cognitive Search activiteiten](search-monitor-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (zoals rekenkracht, opslag, netwerk, poorten, protocollen, en dergelijke) in uw abonnementen op te vragen en te ontdekken.

Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources in uw abonnementen.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources met metagegevens om ze logisch te organiseren in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Definieer een lijst met goedgekeurde Azure-resources met betrekking tot indexering en verwerking van vaardighedensets in Cognitive Search.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** het is raadzaam om een inventaris van Azure-resources te definiëren die eerder zijn goedgekeurd voor gebruik volgens het beleid en de standaarden van uw organisatie, en vervolgens te controleren op niet-goedgekeurde Azure-resources met Azure Policy of Azure Resource Graph.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure Resource Graph om resources in uw abonnement(en) op te vragen of te ontdekken. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](/azure/governance/policy/samples/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.  

 
Toegang tot de sleutels die worden gebruikt voor het verifiëren van aanvragen voor alle andere bewerkingen, met name die met betrekking tot inhoud met Cognitive Search.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte 'Microsoft.Search' om aangepaste beleidsregels te maken om de configuratie van uw Azure Cognitive Search controleren of afdwingen. U kunt ook ingebouwde definities Azure Policy voor Cognitive Search services, zoals:

Auditlogboekregistratie voor Azure-resources inschakelen

Azure Resource Manager heeft de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie.

U kunt ook de aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](security-controls-policy.md)

- [Beschikbare aliassen Azure Policy weergeven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy effecten [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Cognitive Search-servicebronnen. 

Azure Resource Manager kunnen worden gebruikt voor het onderhouden van de beveiligingsconfiguratie van uw Azure-resources die uw organisatie nodig heeft. 

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](security-controls-policy.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/index)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Cognitive Search-servicebronnen met behulp Azure Policy. 

Gebruik aliassen om aangepaste beleidsregels te maken om de netwerkconfiguraties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op uw specifieke resources. 

Daarnaast kunt u een Azure Automation om configuratiewijzigingen te implementeren en beleids uitzonderingen te beheren. 

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](security-controls-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Security Center om basislijnscans van uw Cognitive Search service-resources uit te voeren.  Gebruik daarnaast Azure Policy om uw resourceconfiguraties te waarschuwen en te controleren. 

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](security-controls-policy.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik beheerde Azure-identiteiten in combinatie met Azure Key Vault om geheimbeheer voor uw cloudtoepassingen te vereenvoudigen.

- [Beheerde identiteiten gebruiken voor Azure-resources](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Een Key Vault](../key-vault/general/quick-create-portal.md)

- [Verificatie met Key Vault met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik een beheerde Azure-identiteit om Cognitive Search toegang te geven tot andere Azure-services, zoals Key Vault- en indexeringsgegevensbronnen, met behulp van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die Ondersteuning biedt voor Azure AD-verificatie, Azure Key Vault, zonder referenties in uw code. 

- [Een indexeringsverbinding met een gegevensbron instellen met behulp van een beheerde identiteit](search-howto-managed-identities-data-sources.md)

- [Door de klant beheerde sleutels configureren voor gegevensversleuteling met behulp van een beheerde identiteit](search-security-manage-encryption-keys.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-resources, zoals Cognitive Search, Blob Storage, Azure SQL Database, en meer. 

Het is uw verantwoordelijkheid om alle inhoud die wordt geüpload naar niet-compute Azure-resources vooraf te scannen. Microsoft heeft geen toegang tot klantgegevens en kan daarom namens u geen antimalwarescans van klantinhoud uitvoeren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antimalwaresoftware en handtekeningen worden bijgewerkt

**Richtlijnen:** niet van toepassing op Cognitive Search. Er kunnen geen antimalwareoplossingen op de resources worden geïnstalleerd. Voor het onderliggende platform verwerkt Microsoft het bijwerken van antimalwaresoftware en handtekeningen.  

 
Voor alle rekenbronnen die eigendom zijn van uw organisatie en worden gebruikt in uw zoekoplossing, volgt u de aanbevelingen in Security Center Compute Apps om ervoor te zorgen dat alle eindpunten zijn bijgewerkt met de nieuwste &amp; handtekeningen. Gebruik voor Linux een antimalwareoplossing van derden.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** er kan geen back-up worden gemaakt van inhoud die is opgeslagen in een zoekservice via Azure Backup of een ander ingebouwd mechanisme, maar u kunt een index opnieuw opbouwen op basis van de broncode van de toepassing en primaire gegevensbronnen, of een aangepast hulpprogramma bouwen om geïndexeerde inhoud op te halen en op te slaan. 

 
- [Voorbeeld van GitHub Index-backup-restore](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Cognitive Search ondersteunt momenteel geen automatische back-up voor gegevens in een zoekservice en moet een back-up worden gemaakt via een handmatig proces. U kunt ook back-up maken van door de klant beheerde sleutels in Azure Key Vault.
 

- [Een back-up maken van een Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Back-ups maken Key Vault sleutels in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Cognitive Search ondersteunt momenteel geen automatische back-up voor gegevens in een zoekservice en moet een back-up worden gemaakt en hersteld via een handmatig proces. Voer regelmatig gegevensherstel uit van inhoud waar u handmatig een back-up van hebt gemaakt om de end-to-end-integriteit van uw back-upproces te garanderen.

- [Een back-up maken van een Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Sleutels Key Vault herstellen in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Cognitive Search ondersteunt momenteel geen automatische back-up voor gegevens in een zoekservice en moet een back-up worden gemaakt via een handmatig proces.  U kunt ook back-up maken van door de klant beheerde sleutels in Azure Key Vault.  

 
Schakel de beveiliging voor zacht verwijderen en ops manier in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van handmatige back-ups, kunt u de functie voor het opslaan en herstellen van uw gegevens inschakelen wanneer blobs of blob-momentopnamen worden verwijderd. 
 

 
- [Een back-up maken van een Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Voorlopig verwijderen en bescherming tegen opschonen in Azure Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md) 

- [Zacht verwijderen voor Azure Blob-opslag](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Ontwikkel een handleiding voor het reageren op incidenten voor uw organisatie. Zorg ervoor dat er plannen voor het reageren op incidenten zijn waarin alle rollen van personeel worden bepaald, evenals de fasen van incidentafhandeling en -beheer, van detectie tot incidentbeoordeling.

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het zoeken of de analytische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit zit die tot de waarschuwing heeft geleid.

Daarnaast kunt u abonnementen markeren met behulp van tags en een naamgevingssysteem maken om Azure-resources te identificeren en categoriseren, met name de resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de kritiekheid van de Azure-resources en -omgeving waar het incident zich heeft voorgedaan.

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefen regelmatig om de mogelijkheden voor het reageren op incidenten van uw systemen te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST, 'Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities' (Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Security Center waarschuwingen en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

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

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
