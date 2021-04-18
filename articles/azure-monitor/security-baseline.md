---
title: Azure-beveiligingsbasislijn voor Azure Monitor
description: De Azure Monitor beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600230"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azure-beveiligingsbasislijn voor Azure Monitor

Met deze beveiligingsbasislijn worden richtlijnen van [de Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Azure Monitor. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Monitor. **Besturingselementen** die niet van toepassing zijn op Azure Monitor of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten.

Zie het volledige Azure Monitor toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Monitor volledig is toegewezen aan de Azure [Security-Azure Monitor benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Schakel Azure Private Link toegang tot Azure SaaS Services (bijvoorbeeld Azure Monitor) en in Azure gehoste services van klanten/partners toe via een privé-eindpunt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden.

Als u wilt dat verkeer toegang Azure Monitor, gebruikt u de servicetags 'AzureMonitor' om inkomende en uitgaande verkeer via netwerkbeveiligingsgroepen toe te staan. Gebruik de servicetag ApplicationInsightsAvailability voor al het inkomende verkeer via netwerkbeveiligingsgroepen om toe te staan dat het verkeer van de beschikbaarheidsbewaking Azure Monitor bereiken. Als u wilt toestaan dat waarschuwingsmeldingen eindpunten van klanten bereiken, gebruikt u de servicetag ActionGroup om inkomende verkeer via netwerkbeveiligingsgroepen toe te staan.

Met regels voor virtuele netwerken Azure Monitor alleen communicatie accepteren die vanuit geselecteerde subnetten in een virtueel netwerk wordt verzonden.

Gebruik De Log Analytics-gateway om gegevens te verzenden naar een Log Analytics-werkruimte in Azure Monitor namens de computers die niet rechtstreeks verbinding kunnen maken met internet, waardoor computers niet hoeven te worden verbonden met internet. 

- [Een Private Link instellen voor Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Computers zonder internettoegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en logboeken

**Richtlijnen:** Azure Monitor een kernservice is en geen ondersteuning biedt voor het rechtstreeks implementeren in een virtueel netwerk, wordt de onderliggende infrastructuur verwerkt door Microsoft. Voor resources die echter netwerkverbindingen maken met de Azure Monitor, beveiligt u hun netwerk met een netwerkbeveiligingsgroep. Schakel stroomlogboeken van netwerkbeveiligingsgroep in en verzend logboeken naar een opslagaccount voor verkeerscontrole. U kunt ook stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

Wanneer u Azure Monitor met Private Link, krijgt u toegang tot netwerklogregistratie, zoals 'Gegevens verwerkt door het privé-eindpunt (IN/OUT)'.

- [Netwerkvereisten voor Azure Monitor agents](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Computers zonder internettoegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** als u wilt dat verkeer toegang Azure Monitor, gebruikt u de servicetags 'AzureMonitor' om inkomende en uitgaande verkeer via netwerkbeveiligingsgroepen toe te staan. Gebruik de servicetag ApplicationInsightsAvailability voor al het inkomende verkeer via netwerkbeveiligingsgroepen om toe te staan dat het verkeer van de beschikbaarheidsbewaking de Azure Monitor bereiken. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Azure Monitor maakt deel uit van de Kernservices van Azure en kan niet afzonderlijk als een service worden geïmplementeerd. Azure Monitor-onderdelen, waaronder de Azure Monitor-agent en Application Insights-SDK, kunnen samen met uw resources worden geïmplementeerd. Dit kan van invloed zijn op de beveiligingsstatus van deze resources.

- [Netwerkvereisten voor Azure Monitor agents](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Computers zonder internettoegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Zie Aan de slag met Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Webtests voor beschikbaarheid instellen](app/monitor-web-app-availability.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Azure-activiteitenlogboek om resourceconfiguraties te bewaken en wijzigingen in uw netwerkresources met betrekking tot uw Azure Monitor. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in deze kritieke netwerkbronnen plaatsvinden.

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Azure Monitor gebruikt activiteitenlogboeken om wijzigingen in de resources te logboeken. U kunt deze logboeken exporteren naar Azure Storage, Event Hub of een Log Analytics-werkruimte. Logboeken opnemen via Azure Monitor voor het verzamelen van beveiligingsgegevens die zijn gegenereerd door eindpuntapparaten, netwerkbronnen en andere beveiligingssystemen. Binnen Azure Monitor kunt u query's uitvoeren en analyses uitvoeren op de gegevens, Azure Storage Accounts gebruiken voor opslag van logboeken op de lange termijn/archivering.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Interne hostlogboeken van virtuele Azure-machines verzamelen met Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor en SIEM-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Azure Monitor maakt gebruik van activiteitenlogboeken, het activiteitenlogboek wordt automatisch ingeschakeld en registreert bewerkingen die worden uitgevoerd op Azure Monitor-resources, zoals: wie de bewerking heeft gestart, wanneer de bewerking is uitgevoerd, de status van de bewerking en andere nuttige controle-informatie. 

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Logboekregistratie en verschillende logboektypen in Azure begrijpen](/azure/azure-monitor/platform/platform-logs-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor log analytics-werkruimteretentieperiode in op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage accounts voor opslag op lange termijn/archivering van uw logboeken.

- [De gegevensretentieperiode in Log Analytics wijzigen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Bewaarbeleid configureren voor Azure Storage accountlogboeken](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer logboeken op afwijkende gedrag en controleer regelmatig de resultaten. Gebruik Azure Monitor en een Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens.

U kunt ook gegevens inschakelen en inschakelen voor Azure Sentinel siem van derden.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics-query's](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Security Center Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en -gebeurtenissen. U kunt ook gegevens inschakelen en inschakelen om gegevens te Azure Sentinel.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Waarschuwingen voor logboekgegevens van Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, service-principals voor groepen en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal.

- [Een directoryrol krijgen in Azure Active Directory (Azure AD) met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

U kunt ook Just-In-Time/Just-Enough-Access inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services en Azure Resource Manager.

- [Azure AD Privileged Identity Management overzicht](../active-directory/privileged-identity-management/pim-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richtlijnen:** gebruik waar mogelijk eenmalige Azure Active Directory (Azure AD) in plaats van afzonderlijke afzonderlijke referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer.

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en aanbevelingen Azure Security Center identiteit en toegang volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Gebruik een beveiligd, door Azure beheerd werkstation (ook wel een Privileged Access Workstation of PAW genoemd) voor beheertaken waarvoor verhoogde bevoegdheden zijn vereist.

- [Inzicht in beveiligde, door Azure beheerde werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie Azure Active Directory (Azure AD) inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten en -bewaking om te detecteren wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken voor het ontdekken van verouderde accounts. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Toegangsbeoordelingen voor Azure Identity gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met elk SIEM-/bewakingshulpprogramma. U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt de gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Risk and Identity Protection-functies om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** gebruik tags wanneer dit mogelijk is om te helpen bij het bijhouden Azure Monitor resources die gevoelige informatie opslaan of verwerken, zoals uw Log Analytics-werkruimten.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Isolatie implementeren met behulp van afzonderlijke abonnementen en beheergroepen voor afzonderlijke beveiligingsdomeinen, zoals omgevingstype en gegevensgevoeligheidsniveau. U kunt het toegangsniveau beperken tot uw Azure Monitor en gerelateerde resources die uw toepassingen en bedrijfsomgevingen nodig hebben. U kunt de toegang tot Azure Monitor via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Azure Monitor onderhandelt standaard over TLS 1.2. Zorg ervoor dat clients die verbinding maken met uw Azure-resources, kunnen onderhandelen over TLS 1.2 of hoger. 

Application Insights en Log Analytics staan beide toe dat TLS 1.1- en TLS 1.0-gegevens worden opgenomen. Gegevens kunnen worden beperkt tot TLS 1.2 door aan de clientzijde te configureren.

- [Gegevens veilig verzenden met TLS 1.2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure Monitor. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.
Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het in grote lengte om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer (RBAC) van Azure om de toegang tot de Azure Monitor.

- [Rollen, machtigingen en beveiliging in Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Azure Monitor ervoor dat alle gegevens en opgeslagen query's in rust worden versleuteld met behulp van door Microsoft beheerde sleutels (MMK). Azure Monitor biedt ook een optie voor versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw Azure Key Vault en wordt gebruikt door opslag met behulp van door het systeem toegewezen beheerde identiteitsverificatie. Deze door de klant beheerde sleutel (CMK) kan worden beveiligd met software of hardware-HSM.

- [Azure Monitor door de klant beheerde sleutels](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics-gegevensbeveiliging](/azure/azure-monitor/platform/data-security)

- [Verzameling, retentie en opslag van gegevens in Application Insights](app/data-retention-privacy.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in Azure Monitor en gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - microsoft.insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** Gebruik een veelvoorkomende risicoscoreprogramma (bijvoorbeeld Common Vulnerability Scoring System) of de standaardrisicoclassificaties die door uw scanprogramma van derden worden verstrekt.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** Gebruik Azure CLI om query's uit te voeren op Azure Monitor resources binnen uw abonnementen. Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources binnen uw abonnementen.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Uw Azure-abonnementen weergeven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

- [Rollen, machtigingen en beveiliging in Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure Monitor resources die metagegevens geven om ze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om uw resources Azure Monitor te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit het abonnement op tijd.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventarisatie van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt.

Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren.  Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde Azure Monitor resources tijdig uit het abonnement worden verwijderd.  

- [Azure Log Analytics-werkruimte verwijderen](/azure/azure-monitor/platform/delete-workspace)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om te beperken Azure Monitor gerelateerde resources die u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resources Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik aangepaste Azure Policy om de configuratie van uw Azure Monitor resources te controleren of af te dwingen. U kunt ook ingebouwde definities Azure Policy gebruiken als beschikbaar.

Daarnaast biedt Azure Resource Manager de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

Als u APM-mogelijkheden voor live streamen gebruikt, moet u het kanaal beveiligen met een geheime API-sleutel naast de instrumentatiesleutel.

- [APM-Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Beschikbare aliassen Azure Policy weergeven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** Gebruik Azure-beleid [weigeren] en [implementeren indien niet aanwezig] om veilige instellingen af te dwingen voor uw Azure Monitor gerelateerde resources.  Daarnaast kunt u de sjablonen Azure Resource Manager om de beveiligingsconfiguratie van uw Azure Monitor resources te onderhouden die uw organisatie nodig heeft.

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels en Azure Resource Manager sjablonen. Voor toegang tot de resources die u in Azure DevOps beheert, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren Azure Monitor gerelateerde resources met behulp van Azure Policy. Gebruik aangepaste Azure Policy om de beveiligingsconfiguratie van uw Azure Monitor resources te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op uw specifieke resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy aliassen](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Security Center basislijnscans uit te voeren voor uw Azure Monitor resources.  Gebruik daarnaast Azure Policy om Azure-resourceconfiguraties te waarschuwen en te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault voor het vereenvoudigen en beveiligen van geheimbeheer voor ondersteunde Azure Monitor-gerelateerde resources.

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Een Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Verificatie met Key Vault met een beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Azure Monitor resources, zonder referenties in uw code.

- [Beheerde identiteiten configureren voor Azure-resources](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden die moeten worden geüpload naar niet-compute Azure-resources vooraf scannen

**Richtlijnen:** Microsoft Antimalware is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Monitor-gerelateerde resources), maar wordt niet uitgevoerd op uw inhoud. 

Scan vooraf alle bestanden die worden geüpload naar Azure Monitor gerelateerde resources, zoals Log Analytics-werkruimte.

Gebruik Azure Security Center detectie van bedreigingen voor gegevensservices om malware te detecteren die is geüpload naar opslagaccounts. 

- [Informatie over Microsoft Antimalware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Informatie Azure Security Center de detectie van bedreigingen voor gegevensservices](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** gebruik Azure Resource Manager om de Azure Monitor en gerelateerde resources te exporteren in een JSON-sjabloon (JavaScript Object Notation) die kan worden gebruikt als back-up voor Azure Monitor en gerelateerde configuraties.  Gebruik Azure Automation om de back-upscripts automatisch uit te voeren. 

- [Log Analytics-werkruimte beheren met behulp Azure Resource Manager sjablonen](/azure/azure-monitor/samples/resource-manager-workspace)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Over Azure Automation](../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** gebruik Azure Resource Manager om de Azure Monitor en gerelateerde resources te exporteren in een JSON-sjabloon (JavaScript Object Notation) die kan worden gebruikt als back-up voor Azure Monitor en gerelateerde configuraties. Back-up maken van door de klant beheerde Azure Key Vault als Azure Monitor resources gebruikmaken van door de klant beheerde sleutels,

- [Log Analytics-werkruimte beheren met behulp Azure Resource Manager sjablonen](/azure/azure-monitor/platform/template-workspace-configuration)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Zorg ervoor dat u periodiek herstel kunt uitvoeren met behulp Azure Resource Manager sjabloonbestanden. Test het herstel van door de klant beheerde back-upsleutels.

- [Log Analytics-werkruimte beheren met behulp Azure Resource Manager sjablonen](/azure/azure-monitor/samples/resource-manager-workspace)

- [Sleutelkluissleutels herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels, Azure Resource Manager sjablonen. Als u resources wilt beveiligen die u beheert in Azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory, indien geïntegreerd met TFS.  Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om door de klant beheerde sleutels te beveiligen.

Daarnaast kunt u beveiliging Soft-Delete en ops manier inschakelen in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van back Azure Resource Manager sjabloon, kunt u de functie voor het opslaan en herstellen van uw gegevens inschakelen wanneer blobs of blob-momentopnamen worden verwijderd.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Beveiliging voor Soft-Delete en ops manier inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Gebruik de Computer Security Incident Handling Guide van NIST om u te helpen bij het maken van uw eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) met behulp van tags en een naamgevingssysteem maken om Azure-resources duidelijk te identificeren en te categoriseren, met name azure-resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel. 

- [Continue export configureren](../security-center/continuous-export.md) 

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetratietests en Red Team-oefeningen voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
