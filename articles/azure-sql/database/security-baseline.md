---
title: Azure-beveiligingsbasislijn voor Azure SQL Database
description: De Azure SQL Database beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29db5b82d73bf96465581ccd6a663455464bbeb9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599567"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure-beveiligingsbasislijn voor Azure SQL Database

Met deze beveiligingsbasislijn worden richtlijnen van [de Azure Security Benchmark versie 1.0 toegepast](../../security/benchmarks/overview-v1.md) op Azure SQL Database. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security-benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure SQL Database. **Besturingselementen** die niet van Azure SQL Database zijn of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten. Zie het volledige Azure SQL Database toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure SQL Database volledig is toegewezen aan de Azure [Security-Azure Monitor benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** u kunt Azure Private Link toegang verlenen tot Azure PaaS Services (bijvoorbeeld SQL Database) en in Azure gehoste services van klanten/partners via een privé-eindpunt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. 

Gebruik de SQL-servicetags om uitgaand verkeer via Azure SQL Database netwerkbeveiligingsgroepen toe te staan.

Met regels voor virtuele netwerken Azure SQL Database alleen communicatie accepteren die vanuit geselecteerde subnetten binnen een virtueel netwerk wordt verzonden.

- [Private Link instellen voor Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [Service-eindpunten en -regels voor virtuele netwerken gebruiken voor databaseservers](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** gebruik Azure Security Center en herstel aanbevelingen voor netwerkbeveiliging voor het subnet Azure SQL Database server is geïmplementeerd. 

Voor Azure Virtual Machines (VM) die verbinding maakt met uw Azure SQL Database Server-exemplaar, moet u stroomlogboeken van netwerkbeveiligingsgroep (NSG's) inschakelen voor de NSG's die deze VM's beveiligen en logboeken verzenden naar een Azure Storage-account voor verkeerscontrole. 

U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [NSG-stroomlogboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw SQL Server-instanties voor beveiliging tegen gedistribueerde Denial of Service-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

- [DDoS-beveiliging configureren](/azure/virtual-network/manage-ddos-protection)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](/azure/security-center/security-center-alerts-data-services)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** schakel voor Azure Virtual Machines (VM's) die verbinding gaan maken met uw Azure SQL Database-exemplaar stroomlogboeken voor netwerkbeveiligingsgroep (NSG's) in voor de NSG's die deze VM's beveiligen en verzend logboeken naar een Azure Storage-account voor verkeerscontrole. Indien vereist voor het onderzoeken van afwijkende activiteiten, moet u Network Watcher pakketopname inschakelen.

- [NSG-stroomlogboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Advanced Threat Protection (ATP) inschakelen voor Azure SQL Database.  Gebruikers ontvangen een waarschuwing bij verdachte databaseactiviteiten, potentiële beveiligingsproblemen en SQL-injectieaanvallen, evenals afwijkende databasetoegangs- en querypatronen. Advanced Threat Protection integreert ook waarschuwingen met Azure Security Center. 

- [Advanced Threat Protection begrijpen en gebruiken voor Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** Gebruik servicetags voor virtuele netwerken om besturingselementen voor netwerktoegang te definiëren voor netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

Wanneer u service-eindpunten voor Azure SQL Database gebruikt, is uitgaand naar Azure SQL Database openbare IP-adressen vereist: netwerkbeveiligingsgroepen (NSG's) moeten worden geopend voor Azure SQL Database IP-adressen om connectiviteit toe te staan. U kunt dit doen met behulp van NSG-servicetags voor Azure SQL Database.

- [Servicetags met service-eindpunten voor Azure SQL Database](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [Servicetags begrijpen en gebruiken](../../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** Netwerkbeveiligingsconfiguraties definiëren en implementeren voor Azure SQL Database server-exemplaren met Azure Policy. U kunt de naamruimte Microsoft.Sql gebruiken om aangepaste beleidsdefinities te definiëren of een van de ingebouwde beleidsdefinities gebruiken die zijn ontworpen voor Azure SQL Database netwerkbeveiliging van de server. Een voorbeeld van een toepasselijk ingebouwd netwerkbeveiligingsbeleid voor Azure SQL Database-server is: 'SQL Server moet een service-eindpunt voor een virtueel netwerk gebruiken'.

 

Gebruik Azure Blueprints grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Management-sjablonen, op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en beleidsregels, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen en omgevingen en beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen (NSG' en andere resources die betrekking hebben op netwerkbeveiliging en verkeersstroom). Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om de bedrijfs behoeften en/of duur (enzovoort) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag vereisen en de waarde ervan, om ervoor te zorgen dat alle resources worden gemaakt met tags en u op de hoogte te stellen van bestaande resources zonder tag.

U kunt een Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources die betrekking hebben op uw Azure SQL Database server-exemplaren. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Schakel controle voor Azure SQL Database databasegebeurtenissen bij en schrijf deze naar een auditlogboek in uw Azure Storage-account, Log Analytics-werkruimte of Event Hubs.

Bovendien kunt u diagnostische Azure SQL streamen naar Azure SQL-analyse, een cloudoplossing die de prestaties van Azure SQL Databases en Azure SQL Managed Instances op schaal en in meerdere abonnementen bewaakt. Het kan u helpen bij het verzamelen en visualiseren Azure SQL Database prestatiemetrieken en het beschikt over ingebouwde intelligentie voor het oplossen van prestatieproblemen.

- [Controle voor uw Azure SQL Database](/azure/sql-database/sql-database-auditing)

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](/azure/sql-database/sql-database-metrics-diag-logging)

- [Diagnostische gegevens streamen naar Azure SQL-analyse](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel controle in op Azure SQL Database server-exemplaar en kies een opslaglocatie voor de auditlogboeken (Azure Storage, Log Analytics of Event Hub).

- [Controle inschakelen voor Azure SQL Server](/azure/sql-database/sql-database-auditing)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** wanneer u uw logboeken Azure SQL Database in een Log Analytics-werkruimte, stelt u de bewaarperiode voor logboeken in op basis van de nalevingsvoorschriften van uw organisatie.

- [Retentieparameters voor logboeken instellen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** logboeken analyseren en controleren op afwijkende gedragingen en regelmatig de resultaten bekijken. Gebruik Azure Security Center Advanced Threat Protection van uw bedrijf om te waarschuwen over ongebruikelijke activiteiten met betrekking tot uw Azure SQL Database-exemplaar. U kunt ook waarschuwingen configureren op basis van metrische waarden of vermeldingen in het Azure-activiteitenlogboek die betrekking hebben op uw Azure SQL Database-exemplaren.

- [Inzicht in Advanced Threat Protection en waarschuwingen voor Azure SQL Server](/azure/sql-database/sql-database-threat-detection-overview)

- [Aangepaste waarschuwingen configureren voor Azure SQL Database](alerts-insights-configure-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Security Center Advanced Threat Protection voor Azure SQL databases voor bewaking en waarschuwingen over afwijkende activiteiten. Schakel Azure Defender sql in voor uw SQL-databases. Azure Defender voor SQL bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het opsporen en verhelpen van mogelijke beveiligingsproblemen in de database en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging voor uw database.

- [Inzicht in Advanced Threat Protection en waarschuwingen voor Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

- [Sql Azure Defender inschakelen voor Azure SQL Database](azure-defender-for-sql.md)

- [Waarschuwingen beheren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en die kunnen worden opgevraagd. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Active Directory (Azure AD) heeft niet het concept van standaardwachtwoorden. Wanneer u een Azure SQL Database inrichten, is het raadzaam om verificatie te integreren met Azure AD.

- [Azure AD-verificatie configureren en beheren met Azure SQL](/azure/azure-sql/database/authentication-aad-configure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** beleidsregels en procedures maken voor het gebruik van toegewezen beheerdersaccounts. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

- [Inzicht Azure Security Center identiteit en toegang](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** Gebruik een PAW (Privileged Access Workstation) met meervoudige verificatie die is geconfigureerd om u aan te melden bij en azure-resources te configureren.

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

Gebruik Advanced Threat Protection voor Azure SQL Database om afwijkende activiteiten te detecteren die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [De identiteits- en toegangsactiviteit van gebruikers in Azure Security Center](../../security-center/security-center-identity-access.md)

- [Advanced Threat Protection en mogelijke waarschuwingen bekijken](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om de portal en Azure Resource Management alleen toegang te verlenen vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Maak een Azure Active Directory beheerder (Azure AD) voor uw Azure SQL Database servers.

- [Azure AD-verificatie configureren en beheren met Azure SQL](authentication-aad-configure.md)

- [Een Azure AD-instantie maken en configureren](../../active-directory-domain-services/tutorial-create-instance.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik daarnaast Azure Identity-toegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van gebruikers kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Azure Identity Access Reviews gebruiken](../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** configureer Azure Active Directory -verificatie (Azure AD) met Azure SQL en maak diagnostische instellingen voor Azure AD-gebruikersaccounts, en verstuur de auditlogboeken en aanmeldingslogboeken naar een Log Analytics-werkruimte. Configureer de gewenste waarschuwingen in de Log Analytics-werkruimte.

- [Azure AD-verificatie configureren en beheren met Azure SQL](authentication-aad-configure.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection en risicodetecties om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Daarnaast kunt u gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Azure AD-risico-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang moet hebben tot klantgegevens, biedt Azure Klanten-lockbox een interface voor klanten om aanvragen voor toegang tot klantgegevens te beoordelen en goed te keuren of af te wijzen.

- [Meer Klanten-lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Resources moeten worden gescheiden door Vnet/Subnet, op de juiste wijze worden getagd en beveiligd binnen een NSG of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Gebruik Private Link; implementeer Azure SQL server in uw VNet en maak privé verbinding met behulp van privé-eindpunten.

- [Extra Azure-abonnementen maken](/azure/billing/billing-create-subscription)

- [Een Beheergroepen](/azure/governance/management-groups/create)

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

- [Private Link instellen voor Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** voor databases in Azure SQL Database opslaan of verwerken van gevoelige informatie, moet u de database en gerelateerde resources als gevoelig markeren met tags. Configureer Private Link in combinatie met servicetags voor netwerkbeveiligingsgroep op uw Azure SQL Database om exfiltratie van gevoelige informatie te voorkomen.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het veel te ver om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over het configureren Private Link en NSG's om gegevens exfiltratie op uw Azure SQL Database voorkomen](/azure/sql-database/sql-database-private-endpoint-overview)

- [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** gebruik Azure SQL Database functie voor gegevensdetectie en -classificatie. Gegevensdetectie en -classificatie biedt geavanceerde mogelijkheden die zijn ingebouwd Azure SQL Database voor het detecteren, classificeren en labelen van de bescherming van gevoelige &amp; gegevens in uw databases.

- [Gegevensdetectie en -classificatie gebruiken voor Azure SQL Server](/azure/sql-database/sql-database-data-discovery-and-classification)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) voor het authenticeren en beheren van de toegang Azure SQL Database instanties.

- [How to integrate Azure SQL Server with Azure AD for authentication (Een server integreren met Azure AD voor verificatie)](/azure/sql-database/sql-database-aad-authentication)

- [Toegang beheren in Azure SQL Server](/azure/sql-database/sql-database-control-access)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** TDE (Transparent Data Encryption) helpt Azure SQL Database, Azure SQL managed instance en Azure Data Warehouse te beschermen tegen de bedreiging van schadelijke offlineactiviteit door data-at-rest te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. TDE is standaard ingeschakeld voor alle nieuw geïmplementeerde databases in SQL Database en SQL Managed Instance. De TDE-versleutelingssleutel kan worden beheerd door Microsoft of de klant.

- [Transparante gegevensversleuteling beheren en uw eigen versleutelingssleutels gebruiken](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer wijzigingen plaatsvinden in productie-exemplaren van Azure SQL Database en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van beveiligingsleed uitvoeren

**Richtlijnen:** schakel Azure Defender sql in voor Azure SQL Database en volg de aanbevelingen van Azure Security Center over het uitvoeren van evaluaties van beveiligingsprobleem op uw Azure SQL Servers.

- [Evaluaties van beveiligingsleed uitvoeren op Azure SQL Database](/azure/sql-database/sql-vulnerability-assessment)

- [Hoe kan ik Azure Defender sql inschakelen?](azure-defender-for-sql.md)

- [Aanbevelingen voor het Azure Security Center van beveiligingsleeds implementeren](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Periodiek terugkerende scans voor uw Azure SQL Database inschakelen; Hiermee configureert u een evaluatie van beveiligingsleed om automatisch één keer per week een scan op uw database uit te voeren. Er wordt een samenvatting van het scanresultaat verzonden naar de e-mailadressen die u op geeft. Vergelijk de resultaten om te controleren of beveiligingsproblemen zijn verteerd.

- [Een rapport voor de evaluatie van beveiligingsle gegevens exporteren in Azure Security Center](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicobeoordelingen (secure score) die door de Azure Security Center.

- [Inzicht Azure Security Center de secure score](/azure/security-center/security-center-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om query's uit te voeren op alle resources (inclusief Azure SQL Server-exemplaren) binnen uw abonnement(en). Zorg ervoor dat u over de juiste machtigingen (lezen) in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources en metagegevens geven om ze logisch in te delen in een taxonomie.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit het abonnement op tijd.

- [Extra Azure-abonnementen maken](/azure/billing/billing-create-subscription)

- [Een Beheergroepen](/azure/governance/management-groups/create)

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen uw abonnement(en). Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen uw abonnement(en). Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy of Azure Security Center aanbevelingen voor Azure SQL Servers/Databases om beveiligingsconfiguraties voor alle Azure-resources te onderhouden.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.SQL om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure SQL servers en databases.

- [Aanbevelingen herstellen in Azure Security Center](/azure/security-center/security-center-sql-service-recommendations)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** gebruik Azure Key Vault om versleutelingssleutels voor Azure SQL Database Transparent Data Encryption (TDE) op te slaan.

- [Gevoelige gegevens beveiligen die worden opgeslagen in Azure SQL Server en de versleutelingssleutels opslaan in Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Azure Key Vault, zonder referenties in uw code.

- [Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure SQL](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Beheerde identiteiten configureren](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Implementeert referentiescanner om referenties in uw code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op klantinhoud.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-resources, zoals App Service, Data Lake Storage, Blob Storage, Azure SQL Server, enzovoort. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

- [Meer Microsoft Antimalware voor Azure Cloud Services en Virtual Machines](../../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Om uw bedrijf te beschermen tegen gegevensverlies, maakt Azure SQL Database elke 12 uur automatisch volledige databaseback-ups, differentiële databaseback-ups en elke 5 tot 10 minuten back-ups van transactielogboek. De back-ups worden voor alle servicelagen ten minste 7 dagen opgeslagen in RA-GRS-opslag. Alle servicelagen, behalve Basic Support configureerbare bewaarperiode voor back-ups voor herstel naar een bepaald tijdstip, maximaal 35 dagen.

Als u wilt voldoen aan verschillende nalevingsvereisten, kunt u verschillende bewaarperioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslagverbruik is afhankelijk van de geselecteerde frequentie van back-ups en de bewaarperiode(en).

- [Inzicht in back-ups en bedrijfscontinuïteit met Azure SQL Server](/azure/sql-database/sql-database-business-continuity)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Azure SQL Database maakt automatisch de databaseback-ups die tussen 7 en 35 dagen worden bewaard en maakt gebruik van geografisch redundante opslag met leestoegang van Azure (RA-GRS) om ervoor te zorgen dat ze behouden blijven, zelfs als het datacenter niet beschikbaar is. Deze back-ups worden automatisch gemaakt. Schakel indien nodig geografisch redundante back-ups op lange termijn in voor uw Azure SQL Database.

Als u door de klant beheerde sleutels voor Transparent Data Encryption, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

- [Back-ups in Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** zorg ervoor dat u regelmatig gegevens kunt herstellen van inhoud binnen Azure Backup. Test, indien nodig, inhoud herstellen naar een geïsoleerd VLAN. Test het herstel van door de klant beheerde back-upsleutels.

- [Sleutelkluissleutels herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Back-Azure SQL Database herstellen met behulp van herstel naar een bepaald tijdstip](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Schakel het gebruik van Azure Key Vault in om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering.

- [Soft Delete inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Zorg ervoor dat er geschreven plannen voor het reageren op incidenten zijn waarin de rollen van het personeel en de fasen van incidentafhandeling/-beheer worden bepaald.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center wijst een ernst toe aan waarschuwingen om u te helpen bij het prioriteren van de volgorde waarin u voor elke waarschuwing moet zorgen, zodat u er meteen bij kunt komen wanneer een resource is aangetast. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit zit die tot de waarschuwing heeft geleid.

- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor incidentrespons van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor het testen, trainen en trainen van programma's voor IT-abonnementen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.

- [De beveiligingscontactcontacte Azure Security Center instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie Continue export. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
