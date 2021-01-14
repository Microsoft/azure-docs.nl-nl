---
title: Azure-beveiligings basislijn voor Azure Database for MariaDB
description: Azure-beveiligings basislijn voor Azure Database for MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99c02644649e133a7634418a5edaaf228d52f0d3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201277"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure-beveiligings basislijn voor Azure Database for MariaDB

De Azure-beveiligings basislijn voor Azure Database for MariaDB bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp** bij het configureren van een persoonlijke koppeling voor Azure database for MariaDB met privé-eind punten. Met een Private Link kunt u via een privé-eindpunt verbinding maken met verschillende PaaS-services in Azure. Met Azure Private Link worden Azure-services binnen uw persoonlijke virtuele network (VNet) geplaatst. Verkeer tussen uw virtuele netwerk en MariaDB-exemplaar wordt het micro soft-backbone-netwerk verplaatst.

U kunt ook Virtual Network Service-eind punten gebruiken om de netwerk toegang tot uw Azure Database for MariaDB-implementaties te beveiligen en te beperken. Regels voor virtuele netwerken zijn één firewall beveiligings functie waarmee wordt bepaald of uw Azure Database for MariaDB communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken.

U kunt uw Azure Database for MariaDB ook beveiligen met firewall regels. De server firewall voor komt dat de toegang tot uw database server wordt verhinderd totdat u opgeeft welke computers zijn gemachtigd. U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U kunt Firewall regels maken op server niveau.

Persoonlijke koppeling configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

VNet-service-eind punten en VNet-regels maken en beheren in Azure Database for MariaDB server: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Azure Database for MariaDB firewall regels configureren: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: wanneer uw Azure database for MariaDB-server is beveiligd met een persoonlijk eind punt, kunt u virtuele machines in hetzelfde virtuele netwerk implementeren. U kunt een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Persoonlijke koppeling configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics inschakelen en gebruiken: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: geavanceerde bedreigingen beveiliging gebruiken voor Azure database for MariaDB. Geavanceerde bedreigingen beveiliging detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Azure Database for MariaDB-instanties om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

DDoS-beveiliging configureren: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: wanneer uw Azure database for MariaDB-server is beveiligd met een persoonlijk eind punt, kunt u virtuele machines in hetzelfde virtuele netwerk implementeren. U kunt vervolgens een netwerk beveiligings groep (NSG) configureren om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics inschakelen en gebruiken: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: geavanceerde bedreigingen beveiliging gebruiken voor Azure database for MariaDB. Geavanceerde bedreigingen beveiliging detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.
Geavanceerde beveiliging tegen bedreigingen configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw Azure database for MariaDB-instanties, gebruikt u de tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetag naam op te geven (bijvoorbeeld SQL. Westus) in het juiste bron-of doel veld van een regel kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.
Opmerking: Azure Database for MariaDB gebruikt het service label ' micro soft. SQL '.

Meer informatie over het gebruik van service Tags: informatie over het https://docs.microsoft.com/azure/virtual-network/service-tags-overview gebruik van de service code voor Azure database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen en netwerk resources die zijn gekoppeld aan uw Azure database for MariaDB-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. DBforMariaDB ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Database for MariaDB instanties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot netwerken of uw Azure Database for MariaDB-instanties, zoals:

- De DDoS Protection-standaard moet zijn ingeschakeld

- Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers

- De MariaDB-server moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy voor beelden voor netwerken: https://docs.microsoft.com/azure/governance/policy/samples/

Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Gebruik labels voor bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom voor uw MariaDB-instanties om meta gegevens en logische organisaties te bieden.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure database for MariaDB exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.
Gebeurtenissen in het activiteiten logboek van Azure bekijken en ophalen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view waarschuwingen maken in azure monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure database for MariaDB voor tijds tempels in de logboeken.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: Diagnostische instellingen en server logboeken en opname logboeken inschakelen voor het verzamelen van beveiligings gegevens die door uw Azure database for MariaDB-instanties zijn gegenereerd. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en een Analytics-account te gebruiken, en Azure Storage accounts voor lange termijn/archiverings opslag. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.
Server logboeken configureren en openen voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controle logboeken configureren en openen voor Azure Database for MariaDB: een https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal onboarding van Azure-Sentinel uitvoeren: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen in op uw Azure database for MariaDB-instanties voor toegang tot controle-, beveiligings-en Diagnostische logboeken. Zorg ervoor dat u het MariaDB-controle logboek expliciet inschakelt. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. U kunt ook diagnostische instellingen van Azure-activiteiten logboek inschakelen en de logboeken naar dezelfde Log Analytics werk ruimte of hetzelfde opslag account sturen.

Server logboeken configureren en openen voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs audit logboeken voor Azure database for MariaDB configureren en openen: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Diagnostische instellingen configureren voor het Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor voor de log Analytics werk ruimte die wordt gebruikt om uw Azure database for MariaDB logboeken te bewaren, stelt u de retentie periode in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.
De Bewaar parameters voor logboek registratie instellen voor Log Analytics-werk ruimten: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period bron logboeken opslaan in een Azure Storage-account: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken in uw MariaDB-instanties voor afwijkend gedrag. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Voor meer informatie over de Log Analytics-werk ruimte: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Richt lijnen**: geavanceerde beveiliging tegen bedreigingen inschakelen voor MariaDB. Advanced Threat Protection voor Azure Database for MariaDB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Daarnaast kunt u Server logboeken en diagnostische instellingen voor MariaDB inschakelen en logboeken naar een Log Analytics-werk ruimte verzenden. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.

Geavanceerde beveiliging tegen bedreigingen inschakelen voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Server logboeken configureren en openen voor MariDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controle logboeken voor MariaDB configureren en openen: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: n.v.t.; MariaDB verwerkt of produceert geen anti-malware-gerelateerde Logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: n.v.t.; MariaDB verwerkt of produceert geen aan DNS gerelateerde Logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: n.v.t.; Bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud een inventaris van de gebruikers accounts met beheerders toegang tot het beheer vlak (Azure Portal/Azure Resource Manager) van uw MariaDB-instanties. Houd bovendien een inventarisatie bij van de beheerders accounts die toegang hebben tot het gegevens vlak van uw MariaDB-instanties. (Bij het maken van de MariaDB-server geeft u referenties op voor een beheerders gebruiker. Deze beheerder kan worden gebruikt om aanvullende MariaDB-gebruikers te maken.)

Meer informatie over toegangs beheer voor MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Meer informatie over ingebouwde rollen van Azure voor Azure-abonnementen: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory heeft niet het concept van standaard wachtwoorden.

Wanneer de MariaDB-resource zelf wordt gemaakt, dwingt Azure het maken van een gebruiker met beheerders rechten af met een sterk wacht woord. Als het MariaDB-exemplaar eenmaal is gemaakt, kunt u echter het eerste account voor de server beheerder gebruiken dat u hebt gemaakt om aanvullende gebruikers te maken en beheerders toegang te verlenen. Wanneer u deze accounts maakt, moet u een ander, sterk wacht woord configureren voor elk account.

Aanvullende accounts maken voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts die toegang hebben tot uw MariaDB-instanties. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Meer informatie over Azure Security Center identiteit en toegang: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: de toegang tot MariaDB wordt bepaald door identiteiten die zijn opgeslagen in de data base en biedt geen ondersteuning voor SSO. De toegang tot het beheer vlak voor MariaDB is beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token dat u hebt verkregen via Azure Active Directory.

Meer informatie over Azure Database for MariaDB REST API: https://docs.microsoft.com/rest/api/mariadb/

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

Meer informatie over privileged Access workstations: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Richt lijnen**: geavanceerde bedreigingen beveiliging inschakelen voor MariaDB om waarschuwingen te genereren voor verdachte activiteiten.

Daarnaast kunt u Azure AD Privileged Identity Management (PIM) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

Geavanceerde bedreigings beveiliging instellen voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Privileged Identity Management (PIM) implementeren: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Meer informatie over Azure AD-risico detectie: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's om de toegang tot Azure-resources zoals MariaDB te beperken.

Benoemde locaties configureren in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Aad) als centrale verificatie-en autorisatie systeem. AAD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en tijdens de overdracht. AAD bevat ook zouten, hashes en veilige gebruikers referenties.

Azure AD-verificatie kan niet worden gebruikt voor directe toegang tot het MariaDB-gegevens vlak. Azure AD-referenties kunnen echter worden gebruikt voor beheer op het niveau van het beheer vlak (bijvoorbeeld de Azure Portal) om MariaDB-beheerders accounts te beheren.

Beheerders wachtwoord voor MariaDB bijwerken: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: raadpleeg de Azure Active Directory-Logboeken om verouderde accounts te detecteren, die kunnen bestaan uit de MariaDB-beheerders rollen. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot MariaDB en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd, bijvoorbeeld elke 90 dagen, om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Meer informatie over Azure AD Reporting: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access revisies gebruiken: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: Diagnostische instellingen inschakelen voor MariaDB en Azure Active Directory, waarbij alle logboeken worden verzonden naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren (zoals mislukte verificatie pogingen) binnen Log Analytics werk ruimte.

Server logboeken configureren en openen voor MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controle logboeken voor MariaDB configureren en openen: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Azure-activiteiten logboeken integreren in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Richt lijnen**: geavanceerde beveiliging tegen bedreigingen inschakelen voor MariaDB. Advanced Threat Protection voor Azure Database for MariaDB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Gebruik de functies voor identiteits beveiliging en risico detectie van Azure Active Directory om automatische reacties op gedetecteerde verdachte acties te configureren. U kunt automatische antwoorden via Azure Sentinel inschakelen voor het implementeren van de beveiligings reacties van uw organisatie.

Geavanceerde beveiliging tegen bedreigingen inschakelen voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Risico beleid voor identiteits beveiliging configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Hoe kan ik Risk ante aanmeldingen voor Azure AD bekijken: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing; Klanten-lockbox nog niet ondersteund voor Azure Database for MariaDB.

Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure database for MariaDB instanties of gerelateerde resources die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Gebruik een combi natie van persoonlijke koppelingen, service-eind punten en/of MariaDB firewall regels om netwerk toegang tot uw MariaDB-instanties te isoleren en te beperken.

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

Persoonlijke koppeling configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Service-eind punten voor Azure Database for MariaDB configureren: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Firewall regels configureren voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: wanneer u virtuele Azure-machines gebruikt om toegang te krijgen tot MariaDB-instanties, maakt u gebruik van een persoonlijke koppeling, MariaDB netwerk configuraties, netwerk beveiligings groepen en service tags om de kans op gegevens exfiltration te verminderen.

Micro soft beheert de onderliggende infra structuur voor MariaDB en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Het beperken van gegevens exfiltration voor Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Azure database for MariaDB biedt ondersteuning voor het verbinden van uw Azure database for MariaDB server met client toepassingen die gebruikmaken van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). Het afdwingen van TLS-verbindingen tussen uw database server en uw client toepassingen helpt bij het beveiligen van ' man in het midden ' aanvallen door de gegevens stroom tussen de server en uw toepassing te versleutelen. Controleer in de Azure Portal of ' SSL-verbinding afdwingen ' is ingeschakeld voor al uw MariaDB-instanties.

Versleuteling in transit configureren voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure database for MariaDB. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot de Azure-Data Base voor het MariaDB-beheer vlak (Azure Portal/Azure Resource Manager). Gebruik SQL-query's voor het maken van toegang tot gegevens vlak (binnen de data base zelf) en configureer gebruikers machtigingen.

Azure RBAC configureren: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Gebruikers toegang configureren met SQL voor MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: n.v.t.; deze richt lijn is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor MariaDB en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: de Azure database for MariaDB-service gebruikt de door FIPS 140-2 gevalideerde cryptografische module voor opslag versleuteling van gegevens op rest. Gegevens, met inbegrip van back-ups, worden versleuteld op schijf, met uitzonde ring van tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits code ring opgenomen in azure Storage-versleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.

Meer informatie over versleuteling op rest voor MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure database for MariaDB en andere essentiële of gerelateerde resources.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: momenteel niet beschikbaar; Azure Security Center biedt nog geen ondersteuning voor de evaluatie van beveiligings problemen voor Azure Database for MariaDB-server.


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure database for MariaDB server.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (inclusief Azure database for MariaDB server) in uw abonnement (en) te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Query's maken met Azure resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Meer informatie over Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Tags Toep assen op Azure database for MariaDB server en andere gerelateerde resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure database for MariaDB server en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken bronnen en Azure als geheel.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken bronnen en Azure als geheel.



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6,11: <div>De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken</div>

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, die Azure Database for MariaDB server die gevoelige informatie bevat, niet kan worden gewijzigd.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure database for MariaDB-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. DBforMariaDB ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Database for MariaDB servers te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw Azure Database for MariaDB-servers, zoals:

- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB

Beschik bare Azure Policy aliassen weer geven: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure database for MariaDB servers en gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

[Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. DBforMariaDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. DBforMariaDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure Database for MariaDB instanties en gerelateerde bronnen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor Azure virtual machines of webtoepassingen die worden uitgevoerd op Azure app service wordt gebruikt voor toegang tot uw Azure database for MariaDB servers, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van de server geheim te vereenvoudigen en te beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Integratie met door Azure beheerde identiteiten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: Azure database for MariaDB server biedt momenteel geen ondersteuning voor Azure Active Directory-verificatie voor toegang tot data bases.  Tijdens het maken van de Azure Database for MariaDB-server geeft u referenties op voor een beheerder. Deze beheerder kan worden gebruikt om aanvullende MariaDB-gebruikers te maken.  

Voor Azure Virtual Machines of webtoepassingen die worden uitgevoerd op Azure App Service wordt gebruikt om toegang te krijgen tot uw Azure Database for MariaDB server, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om referenties op te slaan en op te halen voor Azure Database for MariaDB-server.  Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code. Beheerde identiteiten configureren: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm . Integratie met Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity .



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure database for MariaDB server), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB server enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Database for MariaDB server), maar wordt niet uitgevoerd op de inhoud van de klant.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: Azure database for MariaDB volledige, differentiële en back-ups van transactie Logboeken.  Azure Database for MariaDB maakt automatisch server back-ups en slaat ze op in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.  De standaardretentieperiode voor back-ups is zeven dagen. U kunt deze optioneel configureren tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Meer informatie over back-ups voor MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Inzicht in de initiële configuratie van MariaDB: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: met Azure database for MariaDB worden automatisch server back-ups gemaakt en opgeslagen in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen.  Back-ups maken en herstellen zijn essentiële onderdelen van een strategie voor bedrijfscontinuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

Als Key Vault voor gegevens versleuteling op de client gebruikt voor gegevens die zijn opgeslagen op uw MariaDB-server, moet u regel matig automatische back-ups van uw sleutels maken.

Meer informatie over back-ups voor MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Back-ups maken van Key Vault sleutels:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: voer in azure database for MariaDB een herstel uit van de back-ups van de oorspronkelijke server voor periodieke test van back-ups. Er zijn twee soorten herstel beschikbaar: herstel naar een bepaald tijdstip en geo-herstel. Herstel naar een bepaald tijdstip is beschikbaar met de optie redundantie van back-ups en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server. Geo-Restore is alleen beschikbaar als u uw server hebt geconfigureerd voor geo-redundante opslag en u de server kunt herstellen naar een andere regio.

De geschatte duur van de herstel bewerking is afhankelijk van verschillende factoren, zoals de grootte van de data base, het transactie logboek, de netwerk bandbreedte en het totale aantal data bases dat op hetzelfde moment in dezelfde regio wordt hersteld. De herstel tijd is doorgaans minder dan 12 uur.

Meer informatie over back-up en herstel in Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure database for MariaDB volledige, differentiële en back-ups van transactie Logboeken. Met deze back-ups kunt u een server herstellen naar elk gewenst moment binnen de geconfigureerde back-upperiode. De standaardretentieperiode voor back-ups is zeven dagen. U kunt deze optioneel configureren tot 35 dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Meer informatie over back-up en herstel in Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Micro soft Security Response Center anatomie van een incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- De klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen reactie plan voor incidenten: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. 

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- Beveiligings waarschuwingen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Tags gebruiken om uw Azure-resources te organiseren: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- De Azure Security Center Security-contact persoon instellen: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- Continue export configureren: https://docs.microsoft.com/azure/security-center/continuous-export
- Waarschuwingen streamen naar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.
    

Werk stroom automatisering en Logic Apps configureren: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van de Red Teaming-en live site-indringings tests op door micro soft beheerde Cloud infrastructuur,-services en-toepassingen:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)