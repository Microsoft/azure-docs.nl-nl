---
title: Azure-beveiligingsbasislijn voor Synapse Analytics
description: De Synapse Analytics beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1a426a86ddb25733b8425c5887d2c0522f915e2d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587711"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Azure-beveiligingsbasislijn voor Synapse Analytics

Met deze beveiligingsbasislijn worden richtlijnen uit [azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Synapse Analytics. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Synapse Analytics. **Besturingselementen** die niet van Synapse Analytics zijn uitgesloten.

 
Zie het volledige Synapse Analytics toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Synapse Analytics volledig is toegewezen aan de Azure [Synapse Analytics Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** beveilig Azure SQL server naar een virtueel netwerk via Private Link. Azure Private Link kunt u toegang krijgen tot Azure PaaS-services via een privé-eindpunt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst.

Als u verbinding maakt met uw Synapse SQL-pool, kunt u ook het bereik van de uitgaande verbinding met de SQL database beperken met behulp van een netwerkbeveiligingsgroep. Schakel al het verkeer van de Azure-service SQL database via het openbare eindpunt uit door Azure-services toestaan uit te stellen. Zorg ervoor dat er geen openbare IP-adressen zijn toegestaan in de firewallregels.

- [Inzicht Azure Private Link](../private-link/private-link-overview.md)

- [Meer Private Link voor Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligingsconfiguratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** wanneer u verbinding maakt met uw toegewezen SQL-pool en u stroomlogboeken voor netwerkbeveiligingsgroep (NSG) hebt ingeschakeld, worden logboeken verzonden naar een Azure Storage-account voor verkeerscontrole.

U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Advanced Threat Protection (ATP) gebruiken voor Azure Synapse SQL. ATP detecteert afwijkende activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases. Het kan verschillende waarschuwingen activeren, zoals 'Potentiële SQL-injectie' en 'Toegang vanaf ongebruikelijke locatie'. ATP maakt deel uit van de AANBIEDING Advanced Data Security (ADS) en kan worden geopend en beheerd via de centrale SQL ADS-portal.

Schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld Azure Synapse SQL voor beveiliging tegen gedistribueerde Denial of Service-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

- [INZICHT IN ATP voor Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Een Advanced Data Security inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Overzicht van ADS](../azure-sql/database/azure-defender-for-sql.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** wanneer u verbinding maakt met uw toegewezen SQL-pool en u NSG-stroomlogboeken (netwerkbeveiligingsgroep) hebt ingeschakeld, verzendt u logboeken naar een Azure Storage-account voor het controleren van verkeer. U kunt ook stroomlogboeken verzenden naar een Log Analytics-werkruimte of ze streamen naar Event Hubs.  Indien vereist voor het onderzoeken van afwijkende activiteiten, moet u Network Watcher pakketopname inschakelen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Advanced Threat Protection (ATP) gebruiken voor Azure Synapse SQL. ATP detecteert afwijkende activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases en kan verschillende waarschuwingen activeren, zoals 'Potentiële SQL-injectie' en 'Toegang vanaf ongebruikelijke locatie'. ATP maakt deel uit van de ADS-aanbieding (Advanced Data Security) en kan worden geopend en beheerd via de centrale SQL ADS-portal. ATP integreert waarschuwingen ook met Azure Security Center.

- [INZICHT IN ATP voor Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** Servicetags voor virtuele netwerken gebruiken om besturingselementen voor netwerktoegang te definiëren voor netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres-voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Wanneer u een service-eindpunt gebruikt voor uw toegewezen SQL-pool, is uitgaande naar Azure SQL-database openbare IP-adressen vereist: netwerkbeveiligingsgroepen (NSG's) moeten worden geopend voor Azure SQL Database IP's om connectiviteit toe te staan. U kunt dit doen met behulp van NSG-servicetags voor Azure SQL Database.

- [Servicetags met service-eindpunten voor Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** Netwerkbeveiligingsconfiguraties definiëren en implementeren voor resources met betrekking tot uw toegewezen SQL-pool met Azure Policy. U kunt de naamruimte Microsoft.Sql gebruiken om aangepaste beleidsdefinities te definiëren of een van de ingebouwde beleidsdefinities gebruiken die zijn ontworpen voor Azure SQL-database-/servernetwerkbeveiliging. Een voorbeeld van een toepasselijk ingebouwd netwerkbeveiligingsbeleid voor een Azure SQL Database-server is: 'SQL Server moet een service-eindpunt voor een virtueel netwerk gebruiken'.

Gebruik Azure Blueprints om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Management-sjablonen, op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en beleidsregels, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen en omgevingen en beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen (NSG) en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om zakelijke behoeften en/of duur (enzovoort) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag vereisen en de waarde ervan, om ervoor te zorgen dat alle resources worden gemaakt met tags en om u op de hoogte te stellen van bestaande resources zonder tag.

U kunt een Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources die betrekking hebben op uw toegewezen SQL-pool. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Een controlebeleid kan worden gedefinieerd voor een specifieke database of als een standaardserverbeleid in Azure (dat als host voor Azure Synapse). Een serverbeleid is van toepassing op alle bestaande en nieuw gemaakte databases op de server.

Als servercontrole is ingeschakeld, is dit altijd van toepassing op de database. De database wordt gecontroleerd, ongeacht de controle-instellingen van de database.

Wanneer u controle inschakelen, kunt u ze schrijven naar een auditlogboek in uw Azure Storage Account, Log Analytics-werkruimte of Event Hubs.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden.

- [Controle voor uw Azure SQL instellen](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel controle op Azure SQL serverniveau in voor uw toegewezen SQL-pool en kies een opslaglocatie voor de auditlogboeken (Azure Storage, Log Analytics of Event Hubs).

Controle kan zowel op database- als serverniveau worden ingeschakeld en wordt aangeraden om alleen op serverniveau te worden ingeschakeld, tenzij u een afzonderlijke gegevenss sink of retentie voor een specifieke database moet configureren.

- [Controle inschakelen voor Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Controle inschakelen voor uw server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Verschillen in controlebeleid op serverniveau versus databaseniveau](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** wanneer u logboeken met betrekking tot uw toegewezen SQL-pool opslaat in een opslagaccount, Log Analytics-werkruimte of Event Hub, stelt u de bewaarperiode voor logboeken in op basis van de nalevingsvoorschriften van uw organisatie.

- [De levenscyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Retentieparameters voor logboeken instellen in een Log Analytics-werkruimte](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Streaminggebeurtenissen vastleggen in Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** logboeken analyseren en controleren op afwijkende gedragingen en regelmatig de resultaten bekijken. Gebruik Advanced Threat Protection voor Azure SQL Database in combinatie met Azure Security Center om te waarschuwen over ongebruikelijke activiteiten met betrekking tot uw SQL database. U kunt ook waarschuwingen configureren op basis van metrische waarden of vermeldingen in het Azure-activiteitenlogboek die betrekking hebben op uw SQL database.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden.

- [Inzicht in Advanced Threat Protection en waarschuwingen voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Een Advanced Data Security inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Aangepaste waarschuwingen configureren voor Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Gebruik Advanced Threat Protection (ATP) voor Azure SQL Database in combinatie met Azure Security Center om afwijkende activiteiten te bewaken en te waarschuwen. ATP maakt deel uit van de ADS-aanbieding (Advanced Data Security) en kan worden geopend en beheerd via centrale SQL ADS in de portal. ADS bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het opsporen en verhelpen van mogelijke beveiligingsproblemen in de database en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging voor uw database.

U kunt ook gegevens inschakelen en in gebruik nemen om Azure Sentinel.

- [Inzicht in Advanced Threat Protection en waarschuwingen voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Een Advanced Data Security inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** gebruikers worden geverifieerd met Azure Active Directory (Azure AD) of SQL-verificatie.

Wanneer u de Azure SQL implementeert, geeft u een beheerders-aanmelding en een bijbehorend wachtwoord voor die aanmelding op. Dit beheerdersaccount heet Serverbeheerder. U kunt de beheerdersaccounts voor een database identificeren door de Azure Portal openen en naar het tabblad Eigenschappen van uw server of beheerd exemplaar te navigeren. U kunt ook een Azure AD-beheerdersaccount configureren met volledige beheerdersmachtigingen. Dit is vereist als u Azure AD-verificatie wilt inschakelen.

Gebruik voor beheerbewerkingen de ingebouwde Azure-rollen die expliciet moeten worden toegewezen. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Verificatie voor SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Accounts maken voor niet-gebruikers met beheerders beheerdersaccounts](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Een Azure AD-account gebruiken voor verificatie](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Bestaande aanmeldingen en beheerdersaccounts beheren in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Active Directory (Azure AD) heeft niet het concept van standaardwachtwoorden. Bij het inrichten van een toegewezen SQL-pool is het raadzaam om verificatie te integreren met Azure AD. Met deze verificatiemethode verstuurt de gebruiker een gebruikersaccountnaam en vraagt deze de service de referentiegegevens te gebruiken die zijn opgeslagen in Azure AD.

- [Azure AD-verificatie configureren en beheren met Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Inzicht in verificatie in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** beleidsregels en procedures maken voor het gebruik van toegewezen beheerdersaccounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren dat zich via Azure Active Directory (Azure AD) aan melden.

Als u de beheerdersaccounts voor een database wilt identificeren, opent u de Azure Portal en navigeert u naar het tabblad Eigenschappen van uw server of beheerd exemplaar.

- [Inzicht Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Bestaande aanmeldingen en beheerdersaccounts beheren in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richtlijnen:** Gebruik een Azure-app-registratie (service-principal) om een token op te halen dat kan worden gebruikt voor interactie met uw datawarehouse op het besturingsvlak (Azure Portal) via API-aanroepen.

- [Azure REST API's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Uw clienttoepassing (service-principal) registreren bij Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse SQL REST API-informatie](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

- [Inzicht in meervoudige verificatie in Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** Gebruik een PAW (Privileged Access Workstation) met meervoudige verificatie geconfigureerd om u aan te melden bij en Azure-resources te configureren.

- [Meer informatie over Privileged Access-werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

Gebruik Advanced Threat Protection voor Azure SQL Database in combinatie met Azure Security Center om afwijkende activiteiten te detecteren en te waarschuwen die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot databases of deze te misbruiken.

SQL Server audit kunt u servercontroles maken die servercontrolespecificaties voor gebeurtenissen op serverniveau en databasecontrolespecificaties voor gebeurtenissen op databaseniveau kunnen bevatten. Gecontroleerde gebeurtenissen kunnen worden geschreven naar de gebeurtenislogboeken of om bestanden te controleren.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteit van gebruikers bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

- [Bekijk Advanced Threat Protection en mogelijke waarschuwingen](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Aanmeldingen en gebruikersaccounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Inzicht SQL Server controle](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** Gebruik benoemde locaties voor voorwaardelijke toegang om de portal en Azure Resource Management alleen toegang te geven vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Maak een Azure Active Directory (Azure AD)-beheerder voor de Azure SQL Database-server in uw toegewezen SQL-pool.

- [Azure AD-verificatie configureren en beheren met Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om u te helpen verouderde accounts te vinden. Gebruik bovendien Azure AD-toegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van gebruikers kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang blijven hebben.

Wanneer u SQL-verificatie gebruikt, maakt u ingesloten databasegebruikers in de database. Zorg ervoor dat u een of meer databasegebruikers in een aangepaste databaserol zet met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Toegangsbeoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Informatie over aanmeldingen en gebruikersaccounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** configureer Azure Active Directory (Azure AD)-verificatie met Azure SQL en schakel diagnostische instellingen in voor Azure AD-gebruikersaccounts, en verstuur de auditlogboeken en aanmeldingslogboeken naar een Log Analytics-werkruimte. Configureer de gewenste waarschuwingen in Log Analytics.

Wanneer u SQL-verificatie gebruikt, maakt u ingesloten databasegebruikers in de database. Zorg ervoor dat u een of meer databasegebruikers in een aangepaste databaserol zet met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Toegangsbeoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Azure AD-verificatie configureren en beheren met Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aanmeldingen en gebruikersaccounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection en risicodetectiefuncties om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Daarnaast kunt u gegevens on-board en opnemen in Azure Sentinel voor verder onderzoek.

Wanneer u SQL-verificatie gebruikt, maakt u ingesloten databasegebruikers in de database. Zorg ervoor dat u een of meer databasegebruikers in een aangepaste databaserol zet met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Azure AD-risico-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Hoe kunt u de Azure Sentinel](../sentinel/connect-data-sources.md)

- [Aanmeldingen en gebruikersaccounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang moet hebben tot gegevens met betrekking tot de Azure SQL Database in uw toegewezen SQL-pool, biedt Azure Klanten-lockbox een interface voor het controleren en goedkeuren of afwijzen van aanvragen voor gegevenstoegang.

- [Meer Klanten-lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

&amp;Gegevensdetectieclassificatie is ingebouwd in Azure Synapse SQL. De functie biedt geavanceerde mogelijkheden voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw database.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Gegevensdetectieclassificatie &amp; begrijpen](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Resources moeten worden gescheiden door een virtueel netwerk/subnet, op de juiste wijze worden getagd en beveiligd binnen een netwerkbeveiligingsgroep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Gebruik Private Link; implementeer Azure SQL server in een virtueel netwerk en maak veilig verbinding met behulp van Private Link.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Private Link instellen voor Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** voor elke Azure SQL Database in uw toegewezen SQL-pool die gevoelige informatie opslaat of verwerkt, moet u de database en gerelateerde resources als gevoelig markeren met behulp van tags. Configureer Private Link in combinatie met NSG-servicetags (netwerkbeveiligingsgroep) op uw Azure SQL Database-exemplaren om exfiltratie van gevoelige informatie te voorkomen.

Bovendien detecteert Advanced Threat Protection voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse afwijkende activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het veel te ver om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een reeks krachtige besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over het configureren Private Link en NSG's om gegevens exfiltratie op uw Azure SQL Database voorkomen](../azure-sql/database/private-endpoint-overview.md)

- [Inzicht in Advanced Threat Protection voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** gebruik de functie Azure Synapse SQL Data &amp; Discovery-classificatie. Classificatie van gegevensdetectie biedt geavanceerde mogelijkheden die zijn ingebouwd Azure SQL Database voor het detecteren, classificeren en labelen van de bescherming van gevoelige &amp; &amp; gegevens in uw databases.

&amp;Gegevensdetectieclassificatie maakt deel uit van Advanced Data Security-aanbieding, een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. &amp;Gegevensdetectieclassificatie kan worden geopend en beheerd via de centrale SQL ADS-portal.

Daarnaast kunt u een beleid voor dynamische gegevensmaskering (DDM) instellen in de Azure Portal. De engine voor DDM-aanbevelingen markeert bepaalde velden uit uw database als mogelijk gevoelige velden die mogelijk goede kandidaten zijn voor maskering.

- [Gegevensdetectie en -classificatie gebruiken voor Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Inzicht in dynamische gegevensmaskering voor Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Toegangsbeheer van Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de toegang tot Azure SQL databases in uw toegewezen SQL-pool te beheren.

Autorisatie wordt beheerd door de databaserollidmaatschap en machtigingen op objectniveau van uw gebruikersaccount. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen.

- [Een Azure SQL Server integreren met Azure Active Directory (Azure AD) voor verificatie](../azure-sql/database/authentication-aad-overview.md)

- [Toegang beheren in Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Autorisatie en verificatie in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** TDE (Transparent Data Encryption Azure Synapse) helpt uw SQL te beschermen tegen de bedreiging van schadelijke offlineactiviteiten door data-at-rest te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. In Azure is de standaardinstelling voor TDE dat de DEK wordt beveiligd door een ingebouwd servercertificaat. U kunt ook door de klant beheerde TDE gebruiken, ook wel Bring Your Own Key (BYOK) voor TDE genoemd. In dit scenario is de TDE-beveiliging die de DEK versleutelt een door de klant beheerde asymmetrische sleutel die wordt opgeslagen in een door de klant beheerde en beheerde Azure Key Vault (het externe sleutelbeheersysteem in de cloud van Azure) en nooit de sleutelkluis verlaat.

- [Inzicht in door de service beheerde transparante gegevensversleuteling](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Inzicht in door de klant beheerde transparante gegevensversleuteling](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [TDE in-/uit-zetten met behulp van uw eigen sleutel](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Synapse SQL-pools en andere kritieke of gerelateerde resources.

Daarnaast kunt u waarschuwingen instellen voor databases in uw SQL Synapse-pool met behulp van de Azure Portal. Waarschuwingen kunnen u een e-mail sturen of een web hook aanroepen wanneer bepaalde metrische gegevens (bijvoorbeeld databasegrootte of CPU-gebruik) de drempelwaarde bereiken.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Waarschuwingen maken voor Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** schakel Advanced Data Security in en volg aanbevelingen van Azure Security Center het uitvoeren van evaluaties van beveiligingsleeds op Azure SQL Databases.

- [Evaluaties van beveiligingsleed uitvoeren op Azure SQL databases](../azure-sql/database/sql-vulnerability-assessment.md)

- [Het inschakelen van Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

- [Aanbevelingen voor het Azure Security Center van beveiligingsleeds implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Evaluatie van beveiligingsleed is een scanservice die is ingebouwd in Azure Synapse SQL. De service maakt gebruik van een knowledge base met regels die beveiligingsproblemen markeren. Het markeert afwijkingen van best practices, zoals onjuiste configuraties, overmatige machtigingen en onbeveiligde gevoelige gegevens.  Evaluatie van beveiligingsleed kan worden geopend en beheerd via de centrale SQL Advanced Data Security-portal (ADS).

- [Scans voor evaluatie van beveiligingsleed beheren en exporteren in de SQL ADS-portal](../azure-sql/database/sql-vulnerability-assessment.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicoclassificaties (Secure Score) die door de Azure Security Center.

&amp;Gegevensdetectieclassificatie is ingebouwd in Azure Synapse SQL. De functie biedt geavanceerde mogelijkheden voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw database.

- [Inzicht in Azure Security Center secure score](../security-center/secure-score-security-controls.md)

- [Gegevensdetectieclassificatie &amp; begrijpen](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources met betrekking tot uw toegewezen SQL-pool binnen uw abonnement(en) op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Azure Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

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

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Definieer een lijst met goedgekeurde Azure-resources die betrekking hebben op uw toegewezen SQL-pool.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure Resource Graph voor het opvragen/ontdekken van resources binnen uw abonnementen. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen uw abonnement(en). Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy-aliassen in de naamruimte Microsoft.Sql om aangepaste beleidsregels te maken om de configuratie van resources met betrekking tot uw toegewezen SQL-pool te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities voor Azure Databases/Server, zoals:

- Detectie van bedreigingen implementeren op SQL-servers
- SQL Server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** Niet van toepassing; Azure Synapse SQL heeft geen configureerbare beveiligingsinstellingen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** Maak gebruik Azure Security Center basislijnscans uit te voeren voor resources die betrekking hebben op uw toegewezen SQL-pool.

- [Aanbevelingen in een Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Transparent Data Encryption (TDE) met door de klant beheerde sleutels in Azure Key Vault staat versleuteling van de automatisch gegenereerde Database Encryption Key (DEK) toe met een door de klant beheerde asymmetrische sleutel met de naam TDE-beveiliging. Dit wordt in het algemeen ook wel Bring Your Own Key (BYOK)-ondersteuning voor Transparent Data Encryption. In het BYOK-scenario wordt de TDE-beveiliging opgeslagen in een door de klant beheerde Azure Key Vault. Zorg er bovendien voor dat de functie voor zacht verwijderen is ingeschakeld in Azure Key Vault.

- [TDE inschakelen met door de klant beheerde sleutel vanuit Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Zacht verwijderen inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, inclusief Azure Key Vault, zonder referenties in uw code.

- [Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in uw code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden die moeten worden geüpload naar niet-compute Azure-resources vooraf scannen

**Richtlijnen:** Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (Azure Synapse SQL); Het wordt echter niet uitgevoerd op klantinhoud.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-resources, zoals App Service, Data Lake Storage, Blob Storage, Azure SQL Server, enzovoort. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

- [Meer Microsoft Antimalware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** momentopnamen van uw toegewezen SQL-pool worden automatisch de hele dag gemaakt om herstelpunten te maken die gedurende zeven dagen beschikbaar zijn. Deze bewaarperiode kan niet worden gewijzigd. Toegewezen SQL-pool ondersteunt een RPO (Recovery Point Objective) van acht uur. U kunt uw datawarehouse in de primaire regio herstellen op basis van een van de momentopnamen die in de afgelopen zeven dagen zijn gemaakt. Houd er rekening mee dat u indien nodig ook handmatig momentopnamen kunt activeren.

- [Back-up en herstel in toegewezen SQL-pool](sql-data-warehouse/backup-and-restore.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** momentopnamen van uw datawarehouse worden automatisch de hele dag gemaakt om herstelpunten te maken die gedurende zeven dagen beschikbaar zijn. Deze bewaarperiode kan niet worden gewijzigd. Toegewezen SQL-pool ondersteunt een RPO (Recovery Point Objective) van acht uur. U kunt uw datawarehouse in de primaire regio herstellen op basis van een van de momentopnamen die in de afgelopen zeven dagen zijn gemaakt. Houd er rekening mee dat u indien nodig ook handmatig momentopnamen kunt activeren.

Als u een door de klant beheerde sleutel gebruikt om uw databaseversleutelingssleutel te versleutelen, moet u ervoor zorgen dat er een back-up van uw sleutel wordt gemaakt.

- [Back-up en herstel in toegewezen SQL-pool](sql-data-warehouse/backup-and-restore.md)

- [Een back-up maken Azure Key Vault sleutels](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** test regelmatig uw herstelpunten om te controleren of uw momentopnamen geldig zijn. Als u een bestaande toegewezen SQL-pool wilt herstellen vanaf een herstelpunt, kunt u de Azure Portal of PowerShell gebruiken. Herstel van door de klant beheerde sleutels met een back-up testen.

- [Uw sleutels Azure Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Back-up en herstel in toegewezen SQL-pool](sql-data-warehouse/backup-and-restore.md)

- [Een bestaande toegewezen SQL-pool herstellen](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** in Azure SQL Database kunt u één of een pooldatabase met een langetermijnretentiebeleid voor back-ups (LTR) configureren om de databaseback-ups automatisch tot tien jaar in afzonderlijke Azure Blob Storage-containers te bewaren. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de sterkste blokversleutelingen die beschikbaar zijn en compatibel zijn met FIPS 140-2.

Gegevens in een opslagaccount worden standaard versleuteld met door Microsoft beheerde sleutels. U kunt vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw gegevens of u kunt versleuteling beheren met uw eigen sleutels. Als u uw eigen sleutels beheert met Key Vault, moet u ervoor zorgen dat de functie voor soft-delete is ingeschakeld.

- [Langetermijnretentie Azure SQL Database back-ups beheren](../azure-sql/database/long-term-backup-retention-configure.md)

- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)

- [Zacht verwijderen inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Zorg ervoor dat er geschreven plannen voor het reageren op incidenten zijn die rollen van personeel en fasen van incidentafhandeling/-beheer definiëren.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center wijst een ernst toe aan waarschuwingen om u te helpen bij het prioriteren van de volgorde waarin u elke waarschuwing volgt, zodat u er meteen bij kunt wanneer een resource is aangetast. De ernst is gebaseerd op hoe zeker Security Center is in het vinden of de metrische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor incidentrespons van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor het testen, trainen en trainen van programma's voor IT-abonnementen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetratietests en Red Team-oefeningen voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligingsonderzoeken

**Richtlijnen:** Volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [U vindt hier meer informatie over de strategie en uitvoering van Microsoft voor Red Teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
