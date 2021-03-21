---
title: Azure-beveiligings basislijn voor Synapse Analytics
description: De beveiligings basislijn van Synapse Analytics biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586851"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Azure-beveiligings basislijn voor Synapse Analytics

Deze beveiligings basislijn is van toepassing op de richt lijnen van [Azure Security Bench Mark versie 1,0](../security/benchmarks/overview-v1.md) naar Synapse Analytics. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Synapse Analytics. **Besturings elementen** die niet van toepassing zijn op Synapse Analytics, zijn uitgesloten.

 
Als u wilt zien hoe Synapse Analytics volledig is toegewezen aan de Azure Security-Bench Mark, raadpleegt u het [volledige bestand met de basis toewijzing van Synapse Analytics Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Beveilig uw Azure-SQL Server naar een virtueel netwerk via een persoonlijke koppeling. Met Azure private link kunt u toegang krijgen tot Azure PaaS Services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst.

U kunt ook bij het maken van verbinding met uw Synapse SQL-groep het bereik van de uitgaande verbinding met de SQL database beperken door een netwerk beveiligings groep te gebruiken. Schakel alle Azure-service verkeer naar het SQL database via het open bare eind punt door de instelling Azure-Services toestaan in te scha kelen. Zorg ervoor dat er geen open bare IP-adressen zijn toegestaan in de firewall regels.

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Informatie over persoonlijke koppelingen voor Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Richt lijnen**: wanneer u verbinding maakt met uw toegewezen SQL-groep en u NSG-stroom Logboeken (netwerk beveiligings groep) hebt ingeschakeld, worden logboeken verzonden naar een Azure Storage account voor het controleren van verkeer.

U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Synapse SQL. ATP detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of om deze te exploiteren en kunnen verschillende waarschuwingen activeren, zoals ' potentiële SQL-injectie ', ' en ' toegang vanaf ongebruikelijke locatie '. ATP maakt deel uit van de aanbieding voor geavanceerde gegevens beveiliging (ADS) en kan worden geopend en beheerd via de centrale SQL ADS-Portal.

Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan Azure Synapse SQL voor beveiliging van gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [Meer informatie over ATP voor Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Geavanceerde gegevens beveiliging inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Overzicht van advertenties](../azure-sql/database/azure-defender-for-sql.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: wanneer u verbinding maakt met uw toegewezen SQL-groep en u NSG-stroom Logboeken (netwerk beveiligings groep) hebt ingeschakeld, kunt u Logboeken verzenden naar een Azure Storage account voor het controleren van het verkeer. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden of ze streamen naar Event Hubs.  Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Network Watcher pakket vastleggen in.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Synapse SQL. ATP detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of om deze te exploiteren en kunnen verschillende waarschuwingen activeren, zoals ' potentiële SQL-injectie ', ' en ' toegang vanaf ongebruikelijke locatie '. ATP maakt deel uit van de aanbieding voor geavanceerde gegevens beveiliging (ADS) en kan worden geopend en beheerd via de centrale SQL ADS-Portal. ATP integreert ook waarschuwingen met Azure Security Center.

- [Meer informatie over ATP voor Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Wanneer u een service-eind punt voor uw toegewezen SQL-groep gebruikt, is uitgaand naar Azure SQL database open bare IP-adressen vereist: netwerk beveiligings groepen (Nsg's) moeten zijn geopend om te Azure SQL Database Ip's om verbinding te kunnen maken. U kunt dit doen met behulp van NSG-service tags voor Azure SQL Database.

- [Informatie over service tags met Service-eind punten voor Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: netwerk beveiligings configuraties voor resources die zijn gerelateerd aan uw toegewezen SQL-groep definiëren en implementeren met Azure Policy. U kunt de naam ruimte ' micro soft. SQL ' gebruiken om aangepaste beleids definities te definiëren of een van de ingebouwde beleids definities te gebruiken die zijn ontworpen voor Azure SQL database/server-netwerk beveiliging. Een voor beeld van een toepasselijk ingebouwd netwerk beveiligings beleid voor Azure SQL Database Server is: ' SQL Server moet een service-eind punt van een virtueel netwerk gebruiken '.

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure resource management-sjablonen, Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en beleid, in één blauw definitie te voorzien. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (NSG) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw toegewezen SQL-groep. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: er kan een controle beleid worden gedefinieerd voor een specifieke data base of als een standaard server beleid in azure (die als host fungeert voor Azure Synapse). Een server beleid is van toepassing op alle bestaande en nieuw gemaakte data bases op de server.

Als server controle is ingeschakeld, is deze altijd van toepassing op de data base. De data base wordt gecontroleerd, ongeacht de controle-instellingen voor de data base.

Wanneer u controle inschakelt, kunt u deze schrijven naar een audit logboek in uw Azure Storage-account, Log Analytics werk ruimte of Event Hubs.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Controle instellen voor uw Azure SQL-resources](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel controle in op het niveau van de Azure SQL-Server voor uw toegewezen SQL-groep en kies een opslag locatie voor de audit Logboeken (Azure Storage, Log Analytics of event hubs).

Controle kan zowel op het niveau van de data base of op de server worden ingeschakeld en wordt voorgesteld om alleen te worden ingeschakeld op server niveau, tenzij u een afzonderlijke gegevens sink of retentie van een bepaalde data base moet configureren.

- [Controle inschakelen voor Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Controle inschakelen voor uw server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Verschillen in het controle beleid op server niveau versus database niveau](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: wanneer u logboeken met betrekking tot uw toegewezen SQL-groep in een opslag Account, log Analytics werk ruimte of event hubs opslaat, stelt u de Bewaar periode voor het logboek in volgens de nalevings voorschriften van uw organisatie.

- [De levenscyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Para meters voor het bewaren van Logboeken instellen in een Log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Streaming-gebeurtenissen vastleggen in Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig resultaten bekijken. Gebruik geavanceerde beveiliging tegen bedreigingen voor Azure SQL Database in combi natie met Azure Security Center om een waarschuwing te ontvangen over ongebruikelijke activiteiten die betrekking hebben op uw SQL database. U kunt ook waarschuwingen configureren op basis van metrische waarden of Azure-activiteiten logboek vermeldingen die betrekking hebben op uw SQL database.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Meer informatie over geavanceerde beveiliging tegen bedreigingen en waarschuwingen voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Geavanceerde gegevens beveiliging inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Aangepaste waarschuwingen configureren voor Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure SQL database in combi natie met Azure Security Center om afwijkende activiteiten te controleren en te waarschuwen. ATP maakt deel uit van de aanbieding voor geavanceerde gegevens beveiliging (ADS) en kan worden geopend en beheerd via centrale SQL-ADVERTISEMENTs in de portal. ADS bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het haleneren en beperken van potentiële database problemen en de detectie van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base.

U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

- [Meer informatie over geavanceerde beveiliging tegen bedreigingen en waarschuwingen voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Geavanceerde gegevens beveiliging inschakelen voor Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: gebruikers worden geverifieerd met Azure Active Directory (Azure AD) of SQL-verificatie.

Wanneer u Azure SQL voor het eerst implementeert, geeft u een beheerders aanmelding en een bijbehorend wacht woord op voor die aanmelding. Dit beheerders account wordt Server beheerder genoemd. U kunt de beheerders accounts voor een Data Base identificeren door de Azure Portal te openen en te navigeren naar het tabblad Eigenschappen van de server of het beheerde exemplaar. U kunt ook een Azure AD-beheerders account met volledige beheerders machtigingen configureren. Dit is vereist als u Azure AD-verificatie wilt inschakelen.

Voor beheer bewerkingen gebruikt u de ingebouwde rollen van Azure die expliciet moeten worden toegewezen. Gebruik de Azure AD Power shell-module om ad-hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Verificatie voor SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Accounts maken voor gebruikers die geen beheerder zijn](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Een Azure AD-account gebruiken voor verificatie](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Bestaande aanmeldingen en beheerders accounts beheren in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory (Azure AD) beschikt niet over het concept van standaard wachtwoorden. Bij het inrichten van een toegewezen SQL-groep wordt u aangeraden verificatie te integreren met Azure AD. Met deze verificatie methode verzendt de gebruiker de naam van een gebruikers account en vraagt de service de referentie gegevens te gebruiken die zijn opgeslagen in azure AD.

- [Azure AD-verificatie configureren en beheren met Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Verificatie begrijpen in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken dat zich aanmeldt via Azure Active Directory (Azure AD).

Als u de beheerders accounts voor een Data Base wilt identificeren, opent u de Azure Portal en navigeert u naar het tabblad Eigenschappen van de server of het beheerde exemplaar.

- [Inzicht in Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Bestaande aanmeldingen en beheerders accounts beheren in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik een Azure-app-registratie (Service-Principal) om een token op te halen dat kan worden gebruikt voor interactie met uw data warehouse op het besturings vlak (Azure Portal) via API-aanroepen.

- [Azure REST-Api's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Uw client toepassing (Service-Principal) registreren met Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informatie over Azure Synapse SQL REST API](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

- [Meer informatie over multi-factor Authentication in Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik een privileged Access-werk station (Paw) met multi-factor Authentication dat is geconfigureerd om Azure-resources aan te melden en te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD)-beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Gebruik geavanceerde beveiliging tegen bedreigingen voor Azure SQL Database in combi natie met Azure Security Center om afwijkende activiteiten op te sporen en te Signa leren op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren.

Met SQL Server controle kunt u Server controles maken, die server audit specificaties kunnen bevatten voor gebeurtenissen op server niveau en specificaties voor database controle voor gebeurtenissen op database niveau. Gecontroleerde gebeurtenissen kunnen worden geschreven naar de gebeurtenis Logboeken of om bestanden te controleren.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Geavanceerde beveiliging tegen bedreigingen en mogelijke waarschuwingen controleren](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Aanmeldingen en gebruikers accounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

- [SQL Server controle begrijpen](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: Gebruik voorwaardelijke toegang met de naam locaties om toegang tot portal-en Azure-resource beheer toe te staan vanaf alleen specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: maak een Azure Active Directory (Azure AD)-beheerder voor de Azure SQL database-server in uw toegewezen SQL-groep.

- [Azure AD-verificatie configureren en beheren met Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van gebruikers kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Bij het gebruik van SQL-verificatie maakt u Inge sloten database gebruikers in de-data base. Zorg ervoor dat u een of meer database gebruikers in een aangepaste databaserol plaatst met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Aanmeldingen en gebruikers accounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp** bij het configureren van Azure Active Directory-verificatie (Azure AD) met Azure SQL en het inschakelen van diagnostische instellingen voor Azure AD-gebruikers accounts, het verzenden van de audit logboeken en aanmeldings logboeken naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren in Log Analytics.

Bij het gebruik van SQL-verificatie maakt u Inge sloten database gebruikers in de-data base. Zorg ervoor dat u een of meer database gebruikers in een aangepaste databaserol plaatst met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Azure AD-verificatie configureren en beheren met Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aanmeldingen en gebruikers accounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) identiteits beveiliging en functies voor risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Daarnaast worden gegevens on-Board en opgenomen in azure Sentinel voor verder onderzoek.

Bij het gebruik van SQL-verificatie maakt u Inge sloten database gebruikers in de-data base. Zorg ervoor dat u een of meer database gebruikers in een aangepaste databaserol plaatst met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- [Azure AD-risico aanmeldingen weer geven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel aan de trein](../sentinel/connect-data-sources.md)

- [Aanmeldingen en gebruikers accounts in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang moet hebben tot gegevens die zijn gerelateerd aan de Azure SQL database in uw toegewezen SQL-groep, biedt Azure klanten-lockbox een interface waarmee u aanvragen voor gegevens toegang kunt controleren en goed keuren of afwijzen.

- [Klanten-lockbox begrijpen](../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Classificatie van gegevens detectie &amp; is ingebouwd in azure Synapse SQL. De functie biedt geavanceerde mogelijkheden voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw database.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Classificatie van gegevens detectie begrijpen &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Persoonlijke koppeling gebruiken; Implementeer uw Azure-SQL Server in een virtueel netwerk en verbind veilig met persoonlijke koppelingen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Private Link instellen voor Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor elke Azure SQL database in uw toegewezen SQL-groep die gevoelige informatie opslaat of verwerkt, markeert u de data base en gerelateerde resources als gevoelig voor het gebruik van tags. Configureer een persoonlijke koppeling in combi natie met de service tags voor de netwerk beveiligings groep (NSG) op uw Azure SQL Database instanties om te voor komen dat de exfiltration van gevoelige informatie.

Daarnaast biedt geavanceerde bedreigingen voor Azure SQL Database, door Azure SQL beheerd exemplaar en Azure Synapse afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Persoonlijke koppelingen en Nsg's configureren om te voor komen dat gegevens exfiltration in uw Azure SQL Database-instanties](../azure-sql/database/private-endpoint-overview.md)

- [Meer informatie over geavanceerde beveiliging tegen bedreigingen voor Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: gebruik de Azure Synapse SQL- &amp; classificatie functie voor gegevens detectie. &amp;Classificatie van gegevens detectie biedt geavanceerde functies die zijn ingebouwd in Azure SQL database voor het detecteren, classificeren, labelen van &amp; het beveiligen van gevoelige gegevens in uw data bases.

&amp;Classificatie van gegevens detectie maakt deel uit van de geavanceerde gegevens beveiliging. Dit is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Classificatie van gegevens detectie &amp; kan worden geopend en beheerd via de centrale SQL Ads-Portal.

Daarnaast kunt u een beleid voor dynamische gegevens maskering (DDM) instellen in de Azure Portal. De DDM-engine voor aanbevelingen markeert bepaalde velden uit uw data base als mogelijk gevoelige velden die mogelijk goede kandidaten zijn voor maskering.

- [Gegevens detectie en-classificatie gebruiken voor Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Informatie over dynamische gegevens maskering voor Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: Azure RBAC-toegangs beheer gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot Azure SQL-data bases in uw toegewezen SQL-groep.

Autorisatie wordt beheerd door de databaserol lidmaatschappen en object machtigingen van uw gebruikers account. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen.

- [Azure SQL Server integreren met Azure Active Directory (Azure AD) voor verificatie](../azure-sql/database/authentication-aad-overview.md)

- [Toegang beheren in Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Meer informatie over autorisatie en verificatie in Azure SQL](../azure-sql/database/logins-create-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: transparent Data Encryption (TDE) helpt Azure Synapse SQL te beschermen tegen de dreiging van schadelijke offline activiteiten door het versleutelen van gegevens in rust. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. In Azure is de standaard instelling voor TDE dat de DEK wordt beveiligd door een ingebouwd server certificaat. U kunt de door de klant beheerde TDE ook gebruiken, zoals Bring Your Own Key (BYOK)-ondersteuning voor TDE. In dit scenario is de TDE-Protector die de DEK versleutelt, een door de klant beheerde asymmetrische sleutel, die wordt opgeslagen in een door de klant eigendom en beheerd Azure Key Vault (extern beheer systeem op basis van de cloud van Azure) en de sleutel kluis nooit verlaat.

- [Informatie over door service beheerde transparante gegevens versleuteling](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Informatie over door de klant beheerde transparante gegevens versleuteling](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [TDE inschakelen met uw eigen sleutel](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Synapse SQL-groepen en andere kritieke of verwante resources.

Daarnaast kunt u waarschuwingen instellen voor data bases in uw SQL Synapse-groep met behulp van de Azure Portal. Waarschuwingen kunnen u een e-mail sturen of een webhook aanroepen wanneer bepaalde metrische gegevens (zoals de grootte van de data base of het CPU-gebruik) de drempel waarde hebben bereikt.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Waarschuwingen maken voor Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: geavanceerde gegevens beveiliging inschakelen en aanbevelingen volgen van Azure Security Center over het uitvoeren van evaluatie van beveiligings problemen in Azure SQL-data bases.

- [Beveiligings evaluaties uitvoeren voor Azure SQL-data bases](../azure-sql/database/sql-vulnerability-assessment.md)

- [Geavanceerde gegevens beveiliging inschakelen](../azure-sql/database/azure-defender-for-sql.md)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: evaluatie van beveiligings problemen is een scan service die is ingebouwd in azure Synapse SQL. De service maakt gebruik van een Knowledge Base met regels voor het markeren van beveiligings problemen. Het markeert afwijkingen van aanbevolen procedures, zoals onjuiste configuraties, buitensporige machtigingen en onbeveiligde gevoelige gegevens.  De evaluatie van beveiligings problemen is toegankelijk en wordt beheerd via de centrale SQL Advanced Data Security-Portal (ADS).

- [Scans voor evaluatie van beveiligings problemen in de SQL ADS-portal beheren en exporteren](../azure-sql/database/sql-vulnerability-assessment.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

Classificatie van gegevens detectie &amp; is ingebouwd in azure Synapse SQL. De functie biedt geavanceerde mogelijkheden voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw database.

- [Azure Security Center beveiligde Score begrijpen](../security-center/secure-score-security-controls.md)

- [Classificatie van gegevens detectie begrijpen &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources te doorzoeken en te detecteren die betrekking hebben op uw toegewezen SQL-groep binnen uw abonnement (en). Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via Azure resource Graph, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Definieer een lijst met goedgekeurde Azure-resources die betrekking hebben op uw toegewezen SQL-groep.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources in uw abonnementen. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen te plaatsen voor het type resources dat kan worden gemaakt in klant abonnementen met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen uw abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. SQL ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de configuratie van resources die zijn gerelateerd aan uw toegewezen SQL-groep. U kunt ook gebruikmaken van ingebouwde beleids definities voor Azure-data bases/-servers, zoals:

- Detectie van bedreigingen implementeren op SQL-servers
- SQL Server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: niet van toepassing; Azure Synapse SQL heeft geen Configureer bare beveiligings instellingen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: Maak gebruik van Azure Security Center om basislijn scans uit te voeren voor alle resources die betrekking hebben op uw toegewezen SQL-groep.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: transparent Data Encryption (TDE) met door de klant beheerde sleutels in azure Key Vault staat versleuteling toe van de automatisch gegenereerde database versleutelings sleutel (dek) met een door de klant beheerde asymmetrische sleutel met de naam TDe Protector. Dit wordt ook in het algemeen aangeduid als Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption. In het BYOK-scenario wordt de TDE-Protector opgeslagen in een door de klant eigendom en beheerde Azure Key Vault. Zorg er bovendien voor dat zacht verwijderen is ingeschakeld in Azure Key Vault.

- [TDE inschakelen met door de klant beheerde sleutel van Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Zacht verwijderen inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Key Vault, zonder enige referenties in uw code.

- [Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in uw code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Synapse SQL). het wordt echter niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure SQL Server, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: moment opnamen van uw toegewezen SQL-groep worden automatisch uitgevoerd op de hele dag herstel punten maken die zeven dagen beschikbaar zijn. Deze Bewaar periode kan niet worden gewijzigd. Een toegewezen SQL-groep ondersteunt een Recovery Point Objective van acht uur. U kunt uw data warehouse in de primaire regio herstellen vanuit een van de moment opnamen die in de afgelopen zeven dagen zijn gemaakt. U kunt ook hand matig moment opnamen activeren als dat nodig is.

- [Back-ups maken en herstellen in toegewezen SQL-groep](sql-data-warehouse/backup-and-restore.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: moment opnamen van uw data warehouse worden automatisch gemaakt tijdens de hele dag, waarbij herstel punten worden gemaakt die zeven dagen beschikbaar zijn. Deze Bewaar periode kan niet worden gewijzigd. Een toegewezen SQL-groep ondersteunt een Recovery Point Objective van acht uur. U kunt uw data warehouse in de primaire regio herstellen vanuit een van de moment opnamen die in de afgelopen zeven dagen zijn gemaakt. U kunt ook hand matig moment opnamen activeren als dat nodig is.

Als u een door de klant beheerde sleutel gebruikt om de database versleutelings sleutel te versleutelen, moet u controleren of er een back-up van de sleutel wordt gemaakt.

- [Back-ups maken en herstellen in toegewezen SQL-groep](sql-data-warehouse/backup-and-restore.md)

- [Back-ups maken van Azure Key Vault sleutels](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: test uw herstel punten regel matig om ervoor te zorgen dat uw moment opnamen geldig zijn. Als u een bestaande, specifieke SQL-groep wilt herstellen vanaf een herstel punt, kunt u de Azure Portal of Power shell gebruiken. Het herstellen van een back-up van door de klant beheerde sleutels testen.

- [Azure Key Vault sleutels herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Back-ups maken en herstellen in toegewezen SQL-groep](sql-data-warehouse/backup-and-restore.md)

- [Een bestaande toegewezen SQL-groep herstellen](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: in Azure SQL database kunt u een enkele of een gegroepeerde Data Base configureren met een Bewaar beleid voor lange termijn back-ups (LTR) voor het automatisch bewaren van back-ups van de data base in afzonderlijke Azure Blob Storage-containers voor Maxi maal tien jaar. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2.

Gegevens in een opslag account worden standaard versleuteld met door micro soft beheerde sleutels. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van uw gegevens, maar u kunt ook versleuteling beheren met uw eigen sleutels. Als u uw eigen sleutels beheert met Key Vault, moet u ervoor zorgen dat de functie voor voorlopig verwijderen is ingeschakeld.

- [Azure SQL Database lange termijn retentie van back-ups beheren](../azure-sql/database/long-term-backup-retention-configure.md)

- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)

- [Zacht verwijderen inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richt lijnen**: Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die rollen van personeel en fasen van incident handling/Management definiëren.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de metriek die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [U kunt naar de publicatie van het NIST verwijzen: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
