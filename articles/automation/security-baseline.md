---
title: Azure-beveiligingsbasislijn voor Automation
description: De Automation-beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834494"
---
# <a name="azure-security-baseline-for-automation"></a>Azure-beveiligingsbasislijn voor Automation

Met deze beveiligingsbasislijn worden richtlijnen van [azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Azure Automation. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Automation. **Besturingselementen** die niet van Azure Automation zijn uitgesloten.

 
Zie het volledige Azure Automation toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Automation volledig is toegewezen aan de Azure [Azure Automation Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Azure Automation biedt nog geen ondersteuning Azure Private Link voor het beperken van de toegang tot de service via privé-eindpunten. Runbooks die resources verifiëren en uitvoeren in Azure, worden uitgevoerd op een Azure-sandbox en maken gebruik van gedeelde back-endresources, die Microsoft verantwoordelijk is voor het isoleren van elkaar; hun netwerk is onbeperkt en heeft toegang tot openbare resources. Azure Automation momenteel geen integratie van virtuele netwerken voor privénetwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturingselement is niet van toepassing als u de out-of-the-box-service gebruikt zonder Hybrid Runbook Workers.

Voor verdere isolatie voor uw runbooks kunt u Hybrid Runbook Workers gebruiken die worden uitgevoerd op virtuele Azure-machines. Wanneer u een virtuele Azure-machine maakt, moet u een virtueel netwerk (VNet) maken of een bestaand VNet gebruiken en uw VM's configureren met een subnet. Zorg ervoor dat voor alle geïmplementeerde subnetten een netwerkbeveiligingsgroep is toegepast met netwerktoegangsbesturingselementen die specifiek zijn voor vertrouwde poorten en bronnen van uw toepassingen. Raadpleeg de beveiligingsaanbeveling voor die specifieke service voor servicespecifieke vereisten. Als u een specifieke vereiste hebt, kan Azure Firewall ook worden gebruikt om er aan te voldoen.

- [Virtuele netwerken en virtuele machines in Azure](../virtual-machines/network-overview.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

- [Het implementeren en configureren van Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Uitvoeringsomgeving voor runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** Azure Automation momenteel geen integratie van virtuele netwerken voor privénetwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturingselement is niet van toepassing als u de out-of-the-box-service gebruikt zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden gebacked door virtuele Azure-machines, moet u ervoor zorgen dat het subnet met die werksters is ingeschakeld met een netwerkbeveiligingsgroep (NSG) en stroomlogboeken configureert om logboeken door testuren naar een opslagaccount voor verkeerscontrole. U kunt ook NSG-stroomlogboeken doorsturen naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

Hoewel NSG-regels en door de gebruiker gedefinieerde routes niet van toepassing zijn op privé-eindpunten, worden NSG-stroomlogboeken en bewakingsgegevens voor uitgaande verbindingen nog steeds ondersteund en kunnen ze worden gebruikt.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Azure Automation momenteel geen integratie van virtuele netwerken voor privénetwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturingselement is niet van toepassing als u de out-of-the-box-service gebruikt zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden gebacked door virtuele Azure-machines, moet u DDoS (Distributed Denial of Service) Standard-beveiliging inschakelen op uw virtuele netwerken die als host voor uw Hybrid Runbook Workers worden gebruikt om bescherming te bieden tegen DDoS-aanvallen. Met behulp Azure Security Center Integrated Threat Intelligence kunt u communicatie met bekende schadelijke IP-adressen weigeren.  Configureer Azure Firewall in elk van de Virtual Network segmenten, met Bedreigingsinformatie ingeschakeld en configureer deze om te waarschuwen en te weigeren **voor** schadelijk netwerkverkeer.

U kunt Azure Security Center Just-In-Time-netwerktoegang van windows gebruiken om de blootstelling van virtuele Windows-machines aan de goedgekeurde IP-adressen voor een beperkte periode te beperken.  Gebruik ook Azure Security Center aanbevelingen voor adaptieve netwerkharding voor NSG-configuraties om poorten en bron-IP's te beperken op basis van werkelijk verkeer en bedreigingsinformatie.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

- [Inzicht Azure Security Center adaptieve netwerkharding](../security-center/security-center-adaptive-network-hardening.md)

- [Meer Azure Security Center Just-In-Time-Access Control](../security-center/security-center-just-in-time.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** Azure Automation momenteel geen integratie van virtuele netwerken voor privénetwerken heeft naast de ondersteuning voor Hybrid Runbook Workers, is dit besturingselement niet van toepassing als u de out-of-the-box-service zonder Hybrid Workers gebruikt.

Als u Hybrid Runbook Workers gebruikt met ondersteuning van virtuele Azure-machines, kunt u NSG-stroomlogboeken opnemen in een opslagaccount om stroomrecords te genereren voor uw Azure Virtual Machines die als runbook workers optreden. Bij het onderzoeken van afwijkende activiteiten kunt u Network Watcher inschakelen, zodat netwerkverkeer kan worden gecontroleerd op ongebruikelijke en onverwachte activiteit.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Azure Automation momenteel geen integratie van virtuele netwerken voor privénetwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturingselement is niet van toepassing als u de out-of-the-box-service gebruikt zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden gehost op virtuele Azure-machines, kunt u pakketopnamen van Network Watcher en open source IDS-hulpprogramma's combineren om netwerkindringingsdetectie uit te voeren voor een breed scala aan bedreigingen voor deze werkmachines. U kunt ook Azure Firewall implementeren in de Virtual Network-segmenten, met Bedreigingsinformatie ingeschakeld en geconfigureerd voor 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

- [Netwerkindringingsdetectie uitvoeren met Network Watcher en open source hulpprogramma's](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik Virtual Network voor het definiëren van besturingselementen voor netwerktoegang in netwerkbeveiligingsgroepen of Azure Firewall geconfigureerd in Azure waarvoor toegang tot uw Automation-resources is vereist. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld GuestAndHybridManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die worden gebruikt door Azure Automation met Azure Policy.

U kunt ook Azure Blueprints om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk toepassen op nieuwe abonnementen en de controle en het beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy voorbeelden voor netwerken](../governance/policy/samples/built-in-policies.md#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor NSG's en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om de bedrijfs behoeften en/of duur (enzovoort) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag en waarde vereisen, om ervoor te zorgen dat alle resources worden gemaakt met tags en u op de hoogte te stellen van bestaande resources zonder tag.

U kunt Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om resourceconfiguraties te bewaken en wijzigingen in uw netwerkresources te detecteren. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer wijzigingen in kritieke resources plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboekgegevens doorsturen naar Azure Monitor logboeken om beveiligingsgegevens samen te stellen die zijn gegenereerd door Azure Automation resources. Gebruik Azure Monitor logboekquery's om te zoeken en analyses uit te voeren en gebruik Azure Storage accounts voor langetermijn-/archiveringsopslag. Azure Automation kunt runbook-taakstatus, taakstromen, automationstatusconfiguratiegegevens, updatebeheer en logboeken voor het bijhouden van veranderingen of inventarislogboeken verzenden naar uw Log Analytics-werkruimte. Deze informatie is zichtbaar in de api Azure Portal, Azure PowerShell en Azure Monitor Logs, waarmee u eenvoudige onderzoeken kunt uitvoeren.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel siem van derden. 

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken](automation-manage-send-joblogs-log-analytics.md)

- [DSC integreren met Azure Monitor logboeken](automation-dsc-diagnostics.md)

- [Ondersteunde regio's voor gekoppelde Log Analytics-werkruimte](how-to/region-mappings.md)

- [Query'Updatebeheer logboeken](update-management/query-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie inschakelen voor Azure-resources

**Richtlijnen:** schakel Azure Monitor toegang tot uw audit- en activiteitenlogboeken in, waaronder gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, doeladressen en andere nuttige elementen. 

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Azure Monitor de retentieperiode van uw Log Analytics-werkruimte in op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage accounts voor opslag op lange termijn/archivering.

- [De gegevensretentieperiode in Log Analytics wijzigen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Gegevensretentiedetails voor Automation-accounts](./automation-managing-data.md#data-retention)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** logboeken analyseren en controleren op afwijkende gedrag en regelmatig de resultaten bekijken. Gebruik Azure Monitor logboekquery's om logboeken te controleren en query's uit te voeren op logboekgegevens.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden. 

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Inzicht in logboekquery's in Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Security Center met Azure Monitor voor bewaking en waarschuwingen over afwijkende activiteiten in beveiligingslogboeken en -gebeurtenissen.

U kunt ook gegevens inschakelen en in gebruik nemen om Azure Sentinel.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Waarschuwingen voor Azure Monitor logboekgegevens](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Implementeert een oplossing van derden Azure Marketplace oplossing voor DNS-logboekregistratie op basis van de behoefte van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) ingebouwde beheerdersrollen die expliciet kunnen worden toegewezen en waarop query's kunnen worden uitgevoerd. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te vinden die lid zijn van beheergroepen. Wanneer u Uitvoeren als-accounts voor Automation-accounts voor uw runbooks gebruikt, moet u ervoor zorgen dat deze service-principals ook worden bijgeslagen in uw inventaris, omdat ze vaak verhoogde machtigingen hebben. Verwijder ongebruikte Uitvoeren als-accounts om uw blootgestelde aanvalsoppervlak te minimaliseren.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](delete-run-as-account.md)

- [Een uitvoeren Azure Automation Uitvoeren als-account beheren](manage-runas-account.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Automation account heeft niet het concept van standaardwachtwoorden.  Klanten zijn verantwoordelijk voor toepassingen van derden en Marketplace-services die mogelijk gebruikmaken van standaardwachtwoorden die boven op de service of de Hybrid Runbook Workers worden uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken. Wanneer u Uitvoeren als-accounts voor Automation-accounts voor runbooks gebruikt, moet u ervoor zorgen dat deze service-principals ook worden bijgeslagen in uw inventaris, omdat ze vaak verhoogde machtigingen hebben. Bereik van deze identiteiten met de minst bevoegde machtigingen die ze nodig hebben om ervoor te zorgen dat uw runbooks hun geautomatiseerde proces kunnen uitvoeren. Verwijder ongebruikte Uitvoeren als-accounts om het risico op aanvallen te minimaliseren.

U kunt ook Just-In-Time/Just-Enough-Access inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services en Azure Resource Manager.

- [Meer informatie over Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](delete-run-as-account.md)

- [Een uitvoeren Azure Automation uitvoeren als-account beheren](manage-runas-account.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richtlijnen:** gebruik waar mogelijk eenmalige aanmelding met Azure Active Directory (Azure AD) in plaats van afzonderlijke afzonderlijke referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer.

- [Een aanmelding voor toepassingen in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

- [Azure AD gebruiken voor verificatie bij Azure](automation-use-azure-ad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** GebruikWS met meervoudige verificatie die is geconfigureerd om u aan te melden bij en Azure Automation accountresources in productieomgevingen. 

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts 

**Richtlijnen:** Gebruik Azure Active Directory risicodetecties (Azure AD) om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven. Optioneel kan de klant waarschuwingen Azure Security Center risicodetectie doorsturen naar Azure Monitor configureren van aangepaste waarschuwingen/meldingen met behulp van actiegroepen.

- [Inzicht Azure Security Center risicodetecties (verdachte activiteit)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Actiegroepen configureren voor aangepaste waarschuwingen en meldingen](../azure-monitor/alerts/action-groups.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties 

**Richtlijnen:** Het is raadzaam om benoemde locaties voor voorwaardelijke toegang te gebruiken om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's. 

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op. Als u Hybrid Runbook Workers gebruikt, kunt u gebruikmaken van beheerde identiteiten in plaats van Uitvoeren als-accounts om naadlozere beveiligde machtigingen mogelijk te maken.

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

- [Runbookverificatie gebruiken met beheerde identiteiten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik daarnaast Azure-identiteitstoegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. Wanneer u Uitvoeren als-accounts voor Automation-accounts gebruikt voor uw runbooks, zorgt u ervoor dat deze service-principals ook in uw inventaris worden bijgeslagen, omdat ze vaak verhoogde machtigingen hebben. Verwijder ongebruikte Uitvoeren als-accounts om uw blootgestelde aanvalsoppervlak te minimaliseren.

- [Inzicht in Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](delete-run-as-account.md)

- [Een uitvoeren Azure Automation Uitvoeren als-account beheren](manage-runas-account.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met elk SIEM-/bewakingshulpprogramma.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt de gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Risk and Identity Protection-functies om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten voor uw netwerkresource. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** Voor Azure Automation-accounts heeft Microsoft ondersteuning toegang tot metagegevens van platformresources tijdens een open ondersteuningscase zonder gebruik te maken van een ander hulpprogramma.

Wanneer u Hybrid Runbook Workers gebruikt die worden ondersteund door virtuele Azure-machines en een derde partij toegang moet hebben tot klantgegevens (zoals tijdens een ondersteuningsaanvraag), gebruikt u Klanten-lockbox (preview) voor virtuele Azure-machines om aanvragen voor toegang tot klantgegevens te controleren en goed te keuren of af te wijzen.

- [Informatie over Klanten-lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om te helpen bij het bijhouden Azure Automation resources die gevoelige informatie opslaan of verwerken. 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Omgevingen isoleren met behulp van afzonderlijke Automation-accountbronnen. Resources zoals Hybrid Runbook Workers moeten worden gescheiden door een virtueel netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd binnen een netwerkbeveiligingsgroep (NSG) of Azure Firewall. Voor virtuele machines die gevoelige gegevens opslaan of verwerken, implementeert u beleid en procedures om deze uit te schakelen wanneer deze niet in gebruik zijn.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen of waarschuwingen configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** Wanneer u de functie Hybrid Runbook Worker gebruikt, maakt u gebruik van een oplossing van derden van Azure Marketplace op netwerkperimeters die controleert op onbevoegde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en bescherming tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Versleutel alle gevoelige informatie die onderweg is. Zorg ervoor dat clients die verbinding maken met uw Azure-resources in virtuele Azure-netwerken, kunnen onderhandelen over TLS 1.2 of hoger. Azure Automation ondersteunt en dwingt transport layer (TLS) 1.2 en alle client-aanroepen of latere versies voor alle externe HTPS-eindpunten (via webhooks, DSC-knooppunten, hybride runbook worker).

Volg Azure Security Center aanbevelingen voor versleuteling in rust en versleuteling tijdens overdracht, indien van toepassing.

- [Inzicht in versleuteling tijdens overdracht met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation TLS 1.2 afdwingen](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Gebruik een actief detectieprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologiesystemen van de organisatie, met inbegrip van de informatie die zich op locatie of bij een externe serviceprovider bevindt en werk de inventaris van gevoelige informatie van de organisatie bij.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de toegang tot Azure Automation-resources te beheren met behulp van de ingebouwde roldefinities, en wijs toegang toe aan gebruikers die toegang hebben tot uw automatiseringsbronnen volgens een toegangsmodel met de minste bevoegdheden of 'net genoeg'. Wanneer u Hybrid Runbook Workers gebruikt, maakt u gebruik van beheerde identiteiten voor deze virtuele machines om het gebruik van service-principals te voorkomen. Wanneer u zowel de multitenator als Hybrid Runbook Workers gebruikt, moet u ervoor zorgen dat azure RBAC-machtigingen met een correct bereik worden toegepast op de identiteit van de runbook workers.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Runbookmachtigingen voor een Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Rolmachtigingen en beveiliging beheren](automation-role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Azure Automation wordt momenteel niet beschikbaar gemaakt voor de virtuele machines van runbook worker onderliggende multi-tenant en dit wordt afgehandeld door het platform. Dit besturingselement is niet van toepassing als u de out-of-the-box-service gebruikt zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden gebacked door virtuele Azure-machines, moet u een oplossing voor preventie van gegevensverlies op basis van een externe host gebruiken om toegangsbesturingselementen af te dwingen voor uw gehoste Hybrid Runbook Worker virtuele machines.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Door de klant beheerde sleutels gebruiken met Azure Automation. Azure Automation biedt ondersteuning voor het gebruik van door de klant beheerde sleutels voor het versleutelen van alle 'beveiligde assets' die worden gebruikt, zoals referenties, certificaten, verbindingen en versleutelde variabelen. Maak gebruik van versleutelde variabelen met uw runbooks voor al uw permanente opzoekbehoeften voor variabelen om onbedoelde blootstelling te voorkomen.

Wanneer u Hybrid Runbook Workers gebruikt, worden de virtuele schijven op de virtuele machines in rust versleuteld met versleuteling aan de serverzijde of met Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows om beheerde schijven te versleutelen met door de klant beheerde sleutels binnen de gast-VM. Versleuteling aan de serverzijde met door de klant beheerde sleutels verbetert de ADE doordat u besturingssysteemtypen en -afbeeldingen voor uw VM's kunt gebruiken door gegevens in de Storage-service te versleutelen.

- [Versleuteling aan de serverzijde van beheerde Azure-schijven](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption voor Windows-VM's](../virtual-machines/windows/disk-encryption-overview.md)

- [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Beheerde variabelen in Azure Automation](shared-resources/variables.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Automation:**

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in kritieke Azure-resources, zoals netwerkonderdelen, Azure Automation-accounts en runbooks. 

- [Diagnostische logboekregistratie voor een netwerkbeveiligingsgroep](../private-link/private-link-overview.md#logging-and-monitoring)

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van beveiligingsleed uitvoeren

**Richtlijnen:** Volg aanbevelingen van Azure Security Center uitvoeren van evaluaties van beveiligingsleeds op uw Azure-resources

- [Aanbevelingen voor beveiliging in Azure Security Center](../security-center/security-center-recommendations.md)

- [Security Center naslag voor aanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Scanresultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligingsproblemen zijn ver verhelpen. Wanneer u vulnerability management aanbevolen door Azure Security Center, kan de klant naar de portal van de geselecteerde oplossing draaien om historische scangegevens weer te geven.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicoclassificaties (beveiligingsscore) die door de Azure Security Center om het herstel van gevonden beveiligingsproblemen te prioriteren.

- [Inzicht Azure Security Center de secure score](../security-center/secure-score-security-controls.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph query's uit te voeren en alle Azure Automation binnen uw abonnementen te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources die metagegevens geven om ze logisch te organiseren in een taxonomie. 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om uw resources te Azure Automation bijhouden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd. Verwijder ongebruikte Uitvoeren als-accounts om de blootstelling aan het aanvalsoppervlak te minimaliseren.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](delete-run-as-account.md)

- [Een uitvoeren Azure Automation Uitvoeren als-account beheren](manage-runas-account.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** u moet een inventaris maken van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities: 

- Niet toegestane resourcetypen 
- Toegestane brontypen 

Gebruik daarnaast de Azure Resource Graph om resources in abonnementen op te vragen/te ontdekken. Dit kan helpen bij omgevingen met een hoge beveiliging, zoals omgevingen met opslagaccounts. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy ingebouwde voorbeelden voor Azure Automation](policy-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** De klant kan het maken of gebruiken van resources Azure Policy zoals vereist volgens de bedrijfsrichtlijnen van de klant. U kunt uw eigen proces implementeren voor het verwijderen van niet-geautoriseerde resources. Binnen het Azure Automation is het mogelijk om de PowerShell- of Python-modules te installeren, te verwijderen en te beheren die toegankelijk zijn voor runbooks via de portal of cmdlets. Niet-goedgekeurde of oude module moet worden verwijderd of bijgewerkt voor de runbooks.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Module beheren in Azure Automation](shared-resources/modules.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik beleid voor voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure-beheer vanaf niet-beveiligde of niet-goedgekeurde locaties of apparaten. 

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy om aangepaste beleidsregels te maken om de configuratie van uw Azure Automation en gerelateerde resources te controleren of af te dwingen. U kunt ook ingebouwde definities Azure Policy gebruiken.

Daarnaast biedt Azure Resource Manager de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy ingebouwde voorbeelden voor Azure Automation](policy-reference.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Resource Manager en Azure Policy om Azure-resources die zijn gekoppeld aan Azure Automation. Azure Resource Manager zijn JSON-bestanden die worden gebruikt voor het implementeren van Azure-resources en aangepaste sjablonen moeten veilig worden opgeslagen en onderhouden in een codeopslagplaats. Gebruik de integratiefunctie voor broncodebeheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslagplaats voor broncodebeheer. Gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Integratie van bronbeheer gebruiken](source-control-integration.md)

- [Informatie over het maken Azure Resource Manager sjablonen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Een Automation-account implementeren met behulp van Azure Resource Manager sjabloon](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy ingebouwde voorbeelden voor Azure Automation](policy-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels, Azure Resource Manager-sjablonen en Desired State Configuration scripts. Voor toegang tot de resources die u in Azure DevOps beheert, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS. Gebruik de integratiefunctie voor broncodebeheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslagplaats voor broncodebeheer.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Integratie van bronbeheer gebruiken](source-control-integration.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor Azure-resources met behulp Azure Policy. Gebruik Azure Policy om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-resources te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op uw specifieke resources. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy ingebouwde voorbeelden voor Azure Automation](policy-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy om Azure-resourceconfiguraties te waarschuwen en te controleren. Beleid kan worden gebruikt om te detecteren dat bepaalde resources niet zijn geconfigureerd met een privé-eindpunt.

Wanneer u de functie Hybrid Runbook Worker, gebruikt u Azure Security Center basislijnscans uit te voeren voor uw virtuele Azure-machines.  Aanvullende methoden voor geautomatiseerde configuratie zijn: Azure Automation State Configuration.

- [Aanbevelingen in een Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy ingebouwde voorbeelden voor Azure Automation](policy-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** gebruik Azure Resource Manager om uw Azure Automation en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die kunnen worden gebruikt als back-ups om Azure Automation accounts en gerelateerde resources te herstellen. Gebruik Azure Automation om de API voor het Azure Resource Manager exporteren van sjablonen regelmatig aan te roepen.

Gebruik de integratiefunctie voor broncodebeheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslagplaats voor broncodebeheer.

- [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager sjabloonverwijzing voor Azure Automation resources](/azure/templates/microsoft.automation/allversions)

- [Een Automation-account maken met behulp van een Azure Resource Manager sjabloon](quickstart-create-automation-account-template.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resourcegroepen - Sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

- [Inleiding tot Azure Automation](automation-intro.md)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Integratie van bronbeheer gebruiken](source-control-integration.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** gebruik Azure Resource Manager om uw Azure Automation en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die kunnen worden gebruikt als back-ups om Azure Automation accounts en gerelateerde resources te herstellen. Gebruik Azure Automation om de API voor het Azure Resource Manager exporteren van sjablonen regelmatig aan te roepen. Back-up maken van door de klant beheerde sleutels binnen Azure Key Vault. U kunt uw runbooks exporteren naar scriptbestanden met behulp van Azure Portal of PowerShell.

- [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager sjabloonverwijzing voor Azure Automation resources](/azure/templates/microsoft.automation/allversions)

- [Een Automation-account maken met behulp van een Azure Resource Manager sjabloon](quickstart-create-automation-account-template.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resourcegroepen - Sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

- [Inleiding tot Azure Automation](automation-intro.md)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Azure-gegevensback-up voor Automation-accounts](./automation-managing-data.md#data-backup)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** zorg ervoor dat u sjablonen regelmatig regelmatig Azure Resource Manager kunt implementeren naar een geïsoleerd abonnement, indien nodig. Test het herstel van door de klant beheerde back-upsleutels.

- [Resources implementeren met ARM-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Sleutelkluissleutels herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, Azure Resource Manager sjablonen. Als u resources wilt beveiligen die u in Azure DevOps beheert, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS.

Gebruik de integratiefunctie voor broncodebeheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslagplaats voor broncodebeheer.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Integratie van bronbeheer gebruiken](source-control-integration.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit zit die tot de waarschuwing heeft geleid. 

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) met behulp van tags en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren, met name de resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beveiligen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor Azure-resources te identificeren. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen 

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
