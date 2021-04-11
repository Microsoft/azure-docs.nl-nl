---
title: Azure-beveiligings basislijn voor Azure Database for MySQL
description: De Azure Database for MySQL Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62501d6174b9ef8806de0bbdae0dfedfe70da03a
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968033"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Azure-beveiligings basislijn voor Azure Database for MySQL

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure database for MySQL. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure database for MySQL. **Besturings elementen** die niet van toepassing zijn op Azure database for MySQL of waarvoor de verantwoordelijkheid van micro soft is, zijn uitgesloten.

Als u wilt zien hoe Azure Database for MySQL volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure database for MySQL beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp** bij het configureren van een persoonlijke koppeling voor Azure database for MySQL met privé-eind punten. Met een Private Link kunt u via een privé-eindpunt verbinding maken met verschillende PaaS-services in Azure. Met Azure Private Link worden Azure-services binnen uw persoonlijke virtuele network (VNet) geplaatst. Verkeer tussen uw virtuele netwerk en MySQL-exemplaar wordt het micro soft-backbone-netwerk verplaatst.

U kunt ook Virtual Network Service-eind punten gebruiken om de netwerk toegang tot uw Azure Database for MySQL-implementaties te beveiligen en te beperken. Regels voor virtuele netwerken zijn één firewall beveiligings functie die bepaalt of uw Azure Database for MySQL server communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken.

U kunt ook uw Azure Database for MySQL-server beveiligen met firewall regels. De server firewall voor komt dat de toegang tot uw database server wordt verhinderd totdat u opgeeft welke computers zijn gemachtigd. U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U kunt Firewall regels maken op server niveau.

- [Persoonlijke koppelingen voor Azure Database for MySQL configureren](howto-configure-privatelink-portal.md)

- [VNet-service-eind punten en VNet-regels maken en beheren in Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Azure Database for MySQL firewall regels configureren](howto-manage-firewall-using-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: wanneer uw Azure database for MySQL-exemplaar is beveiligd met een persoonlijk eind punt, kunt u virtuele machines in hetzelfde virtuele netwerk implementeren. U kunt een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Persoonlijke koppelingen voor Azure Database for MySQL configureren](howto-configure-privatelink-portal.md)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: geavanceerde bedreigingen beveiliging gebruiken voor Azure database for MySQL. Geavanceerde bedreigingen beveiliging detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Azure Database for MySQL-instanties om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: wanneer uw Azure database for MySQL-exemplaar is beveiligd met een persoonlijk eind punt, kunt u virtuele machines in hetzelfde virtuele netwerk implementeren. U kunt vervolgens een netwerk beveiligings groep (NSG) configureren om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: geavanceerde bedreigingen beveiliging gebruiken voor Azure database for MySQL. Geavanceerde bedreigingen beveiliging detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

- [Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MySQL](howto-database-threat-protection-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw Azure database for MySQL-instanties, gebruikt u Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetag naam op te geven (bijvoorbeeld SQL. Westus) in het juiste bron-of doel veld van een regel kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Opmerking: Azure Database for MySQL gebruikt de service tags van micro soft. SQL.

- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

- [Meer informatie over het gebruik van service tags voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen en netwerk resources die zijn gekoppeld aan uw Azure database for MySQL-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. DBforMySQL ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Database for MySQL instanties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot netwerken of uw Azure Database for MySQL-instanties, zoals:

- De DDoS Protection-standaard moet zijn ingeschakeld

- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](/azure/governance/policy/samples/)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom voor uw Azure database for MySQL-instanties voor het leveren van meta gegevens en logische organisaties.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals **Tags vereisen en de waarde ervan** , om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure database for MySQL exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: Diagnostische instellingen en server logboeken en opname logboeken inschakelen voor het verzamelen van beveiligings gegevens die door uw Azure database for MySQL-instanties zijn gegenereerd. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en een Analytics-account te gebruiken, en Azure Storage accounts voor lange termijn/archiverings opslag. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Informatie over server logboeken voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen in op uw Azure database for MySQL-instanties voor toegang tot logboeken voor controle, trage Query's en MySQL-metrische gegevens. Zorg ervoor dat u het MySQL-controle logboek specifiek inschakelt. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. U kunt ook diagnostische instellingen van Azure-activiteiten logboek inschakelen en de logboeken naar dezelfde Log Analytics werk ruimte of hetzelfde opslag account sturen.

- [Informatie over server logboeken voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Langzame query logboeken voor Azure Database for MySQL configureren en openen](howto-configure-server-logs-in-portal.md)

- [Controle logboeken voor Azure Database for MySQL configureren en openen](howto-configure-audit-logs-portal.md)

- [Diagnostische instellingen configureren voor het Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor voor de log Analytics werk ruimte die wordt gebruikt om uw Azure database for MySQL logboeken te bewaren, stelt u de retentie periode in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Bron logboeken opslaan in een Azure Storage-account](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken vanuit uw Azure database for MySQL-instanties voor afwijkend gedrag. Gebruik de Log Analytics van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Voor meer informatie over de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure database for MySQL. Geavanceerde bedreigingen beveiliging detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Daarnaast kunt u Server logboeken en diagnostische instellingen voor MySQL inschakelen en logboeken naar een Log Analytics-werk ruimte verzenden. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.

- [Advanced Threat Protection voor Azure Database for MySQL inschakelen (preview)](howto-database-threat-protection-portal.md)

- [Informatie over server logboeken voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Langzame query logboeken voor Azure Database for MySQL configureren en openen](howto-configure-server-logs-in-portal.md)

- [Controle logboeken voor Azure Database for MySQL configureren en openen](howto-configure-audit-logs-portal.md)

- [Diagnostische instellingen configureren voor het Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: behoud een inventaris van de gebruikers accounts met beheerders toegang tot het beheer vlak (bijvoorbeeld Azure Portal) van uw Azure data base for MySQLinstances. Daarnaast houdt u een inventarisatie bij van de beheerders accounts die toegang hebben tot het gegevens vlak (binnen de data base zelf) van uw Azure Database for MySQL-exemplaren. (Bij het maken van de MySQL-server geeft u referenties op voor een beheerders gebruiker. Deze beheerder kan worden gebruikt om aanvullende MySQL-gebruikers te maken.)

Azure Database for MySQL biedt geen ondersteuning voor ingebouwde op rollen gebaseerd toegangs beheer, maar u kunt aangepaste rollen maken op basis van specifieke opties voor de resource provider.

- [Informatie over aangepaste rollen voor het Azure-abonnement](../role-based-access-control/custom-roles.md) 

- [Meer informatie over de bewerkingen van Azure Database for MySQL resource provider](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql)

- [Toegangs beheer voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-security#access-management)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory (Azure AD) beschikt niet over het concept van standaard wachtwoorden.

Wanneer de Azure Database for MySQL resource zelf wordt gemaakt, dwingt Azure het maken van een gebruiker met beheerders rechten af met een sterk wacht woord. Zodra het MySQL-exemplaar eenmaal is gemaakt, kunt u echter het eerste server beheerders account dat u hebt gemaakt gebruiken om extra gebruikers te maken en beheerders toegang te verlenen. Wanneer u deze accounts maakt, moet u een ander, sterk wacht woord configureren voor elk account.

- [Aanvullende accounts maken voor Azure Database for MySQL](howto-create-users.md)

- [Beheerders wachtwoord bijwerken](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van speciale beheerders accounts die toegang hebben tot uw Azure database for MySQL-exemplaren. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

- [Inzicht in Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Meer informatie over het maken van gebruikers met beheerders rechten in Azure Database for MySQL](howto-create-users.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: aanmelden bij Azure database for MySQL wordt ondersteund met zowel de gebruikers naam en het wacht woord die rechtstreeks in de Data Base zijn geconfigureerd, en het gebruik van een Azure Active Directory (Azure AD)-identiteit en het gebruik van een Azure AD-token om verbinding te maken. Wanneer u een Azure AD-token gebruikt, worden verschillende methoden ondersteund, zoals een Azure AD-gebruiker, een Azure AD-groep of een Azure AD-toepassing die verbinding maakt met de data base.

De toegang tot het beheer vlak voor MySQL is afzonderlijk beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token die u hebt verkregen van Azure AD.

- [Azure AD gebruiken voor verificatie met Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Azure Database for MySQL REST API begrijpen](/rest/api/mysql/)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. Wanneer u Azure AD-tokens gebruikt om u aan te melden bij uw data base, kunt u hiermee multi-factor Authentication vereisen voor database aanmeldingen.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure AD gebruiken voor verificatie met Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik privileged Access workstations (paw's) met multi-factor Authentication die is geconfigureerd om Azure-resources aan te melden en te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure database for MySQL om waarschuwingen te genereren voor verdachte activiteiten.

Daarnaast kunt u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

- [Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: Gebruik voorwaardelijke toegang met de naam locaties om portal en Azure Resource Manager toegang alleen te bieden vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Als u zich wilt aanmelden Azure Database for MySQL wordt u aangeraden Azure AD te gebruiken en een Azure AD-token toe te staan om verbinding te maken. Wanneer u een Azure AD-token gebruikt, worden verschillende methoden ondersteund, zoals een Azure AD-gebruiker, een Azure AD-groep of een Azure AD-toepassing die verbinding maakt met de data base.

Azure AD-referenties kunnen ook worden gebruikt voor beheer op het niveau van het beheer vlak (bijvoorbeeld de Azure Portal) om MySQL-beheerders accounts te beheren.

- [Azure AD gebruiken voor verificatie met Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Raadpleeg de logboeken van de Azure Active Directory (Azure AD) om verouderde accounts te detecteren die kunnen worden toegevoegd met Azure database for MySQL beheerders rollen. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot Azure Database for MySQL en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd, bijvoorbeeld elke 90 dagen, om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Diagnostische instellingen inschakelen voor Azure Database for MySQL en Azure Active Directory (Azure AD), waarbij alle logboeken worden verzonden naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren (zoals mislukte verificatie pogingen) binnen Log Analytics.

- [Langzame query logboeken voor Azure Database for MySQL configureren en openen](/Azure/mysql/howto-configure-server-logs-in-portal)

- [Controle logboeken voor Azure Database for MySQL configureren en openen](/Azure/mysql/howto-configure-audit-logs-portal)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure database for MySQL om waarschuwingen te genereren voor verdachte activiteiten.

Gebruik de functies voor identiteits beveiliging en risico detectie van Azure Active Directory (Azure AD) om automatische reacties op gedetecteerde verdachte acties te configureren. U kunt automatische antwoorden via Azure Sentinel inschakelen voor het implementeren van de beveiligings reacties van uw organisatie.

U kunt ook logboeken opnemen in azure Sentinel voor verder onderzoek.

- [Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Overzicht van Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure database for MySQL instanties of gerelateerde resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Gebruik een combi natie van persoonlijke koppelingen, service-eind punten en/of firewall regels om netwerk toegang tot uw Azure Database for MySQL-instanties te isoleren en te beperken.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Persoonlijke koppelingen voor Azure Database for MySQL configureren](concepts-data-access-security-private-link.md)

- [VNet-service-eind punten en VNet-regels maken en beheren in Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Azure Database for MySQL firewall regels configureren](concepts-firewall-rules.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: wanneer u Azure-vm's gebruikt om toegang te krijgen tot Azure database for MySQL-exemplaren, maakt u gebruik van een persoonlijke koppeling, MySQL-netwerk configuraties, netwerk beveiligings groepen en service tags om de kans op gegevens exfiltration te beperken.

Micro soft beheert de onderliggende infra structuur voor Azure Database for MySQL en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Het beperken van gegevens exfiltration voor Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Azure database for MySQL ondersteunt het verbinden van uw MySQL-server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld. Zorg ervoor dat in de Azure Portal standaard de instelling SSL-verbinding afdwingen is ingeschakeld voor al uw Azure Database for MySQL exemplaren.

Momenteel worden de TLS-versies ondersteund voor Azure Database for MySQL TLS 1,0, TLS 1,1, TLS 1,2.

- [Versleuteling in transit configureren voor Azure Database for MySQL](concepts-ssl-connection-security.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure database for MySQL. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van de toegang tot het Azure database for MySQL besturings vlak (bijvoorbeeld Azure Portal). Gebruik SQL-query's voor het maken van toegang tot gegevens vlak (binnen de data base zelf) en configureer gebruikers machtigingen. Azure RBAC heeft geen invloed op de gebruikers machtigingen binnen de data base.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Gebruikers toegang configureren met SQL voor Azure Database for MySQL](howto-create-users.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure database for MySQL en andere essentiële of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg de aanbevelingen van Azure Security Center over het beveiligen van uw Azure database for MySQL en gerelateerde bronnen.

Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure Database for MySQL.

- [Azure Security Center aanbevelingen begrijpen](../security-center/recommendations-reference.md)

- [Functie dekking voor Azure PaaS Services in Azure Security Center](../security-center/features-paas.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief Azure database for MySQL instanties) in uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Labels Toep assen op Azure database for MySQL instanties en andere gerelateerde resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure database for MySQL instanties en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken bronnen en Azure als geheel.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure om resources in de abonnementen op te vragen en te detecteren.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](/azure/governance/policy/samples/index)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, zoals exemplaren van Azure Database for MySQL met gevoelige informatie, wordt voor komen.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure database for MySQL-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte **micro soft. DBforMySQL** om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure database for MySQL instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities die betrekking hebben op uw Azure Database for MySQL-instanties, zoals:

SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers

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

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure database for MySQL instanties en gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. DBforMySQL ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte **micro soft. DBforMySQL** om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure Database for MySQL instanties en gerelateerde bronnen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor Azure virtual machines of webtoepassingen die worden uitgevoerd op Azure app service wordt gebruikt om toegang te krijgen tot uw Azure database for MySQL-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van het geheim te vereenvoudigen en te beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault maken](../key-vault/general/quick-create-portal.md)

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: Azure database for MySQL exemplaar ondersteunt de verificatie van Azure Active Directory (Azure AD) voor toegang tot data bases. Tijdens het maken van het Azure Database for MySQL-exemplaar geeft u referenties op voor een beheerders gebruiker. Deze beheerder kan worden gebruikt om extra database gebruikers te maken.

Voor Azure Virtual Machines of webtoepassingen die worden uitgevoerd op Azure App Service wordt gebruikt om toegang te krijgen tot uw Azure Database for MySQL-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om referenties op te slaan en op te halen voor Azure Database for MySQL exemplaar. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure database for MySQL), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: Azure database for MySQL maakt back-ups van de gegevens bestanden en het transactie logboek. Afhankelijk van de ondersteunde maximale opslag grootte, nemen we volledige en differentiële back-ups (4 TB Maxi maal opslag servers) of moment opnamen back-ups (Maxi maal 16 TB aan opslag servers). Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaardretentieperiode voor back-ups is zeven dagen. U kunt deze optioneel configureren tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

- [Meer informatie over back-ups voor Azure Database for MySQL](concepts-backup.md)

- [Azure Database for MySQL initiële configuratie begrijpen](tutorial-design-database-using-portal.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: met Azure database for MySQL worden automatisch server back-ups gemaakt en opgeslagen in lokaal redundante of geografisch redundante opslag, op basis van de keuze van de gebruiker. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering. 

Als Azure Key Vault gebruiken om referenties voor uw Azure Database for MySQL-instanties op te slaan, moet u regel matig automatische back-ups van uw sleutels maken. 

- [Meer informatie over back-ups voor Azure Database for MySQL](howto-restore-server-portal.md) 

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: bij het uitvoeren van een herstel bewerking wordt in azure database for MySQL een nieuwe server gemaakt van de back-ups van de oorspronkelijke server. Er zijn twee soorten herstel beschikbaar: herstel naar een bepaald tijdstip en geo-herstel. Herstel naar een bepaald tijdstip is beschikbaar met de optie redundantie van back-ups en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server. Geo-Restore is alleen beschikbaar als u uw server hebt geconfigureerd voor geo-redundante opslag en u de server kunt herstellen naar een andere regio.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

Test de herstel bewerking van uw Azure Database for MySQL-instanties regel matig.

- [Meer informatie over back-up en herstel in Azure Database for MySQL](concepts-backup.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure database for MySQL volledige, differentiële en back-ups van transactie Logboeken. Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaardretentieperiode voor back-ups is zeven dagen. U kunt deze optioneel configureren tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

- [Meer informatie over back-up en herstel in Azure Database for MySQL](concepts-backup.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

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

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
