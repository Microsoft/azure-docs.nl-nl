---
title: Azure-beveiligings basislijn
description: Azure-beveiligings basislijn voor Azure SQL Database en Azure SQL Managed instance
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 12c7fd1d8ee36b562cd651f50fd0565825441883
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197384"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Azure-beveiligings basislijn voor Azure SQL Database & SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

De Azure-beveiligings basislijn voor Azure SQL Database bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: u kunt persoonlijke Azure-koppeling inschakelen om toegang te krijgen tot Azure PaaS-Services (bijvoorbeeld SQL database) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden.

Als u wilt toestaan dat verkeer wordt bereikt Azure SQL Database, gebruikt u de SQL service-tags om uitgaand verkeer via netwerk beveiligings groepen toe te staan.

Met regels voor virtuele netwerken kunnen Azure SQL Database alleen communicatie accepteren die worden verzonden vanuit geselecteerde subnetten binnen een virtueel netwerk.

Persoonlijke koppeling instellen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Service-eind punten en-regels voor virtuele netwerken gebruiken voor servers:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging voor het subnet waarin uw Azure SQL database wordt geïmplementeerd.

Schakel voor Azure Virtual Machines (VM) die verbinding maken met uw Azure SQL Database, de stroom logboeken voor netwerk beveiligings groepen (NSG) in voor de Nsg's die de virtuele machines beveiligen en logboeken naar een Azure Storage account verzenden voor het controleren van verkeer.

U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informatie over netwerk beveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informatie over netwerk beveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor Azure apps service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Azure SQL database voor beveiliging van gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Richt lijnen**: voor Azure virtual machines (vm's) die verbinding maken met uw Azure SQL database-exemplaar, schakelt u de stroom logboeken voor netwerk beveiligings groepen (NSG) in voor de nsg's die de virtuele machines beveiligt en logboeken naar een Azure Storage account verzenden voor verkeers controle. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Network Watcher pakket vastleggen in.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: Schakel Advanced Threat Protection (ATP) in voor Azure SQL database.  Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten, potentiële kwetsbaar heden en SQL-injectie aanvallen, evenals afwijkende database toegang en query patronen. Geavanceerde bedreigingen beveiliging integreert ook waarschuwingen met Azure Security Center.

Advanced Threat Protection voor Azure SQL Database begrijpen en gebruiken: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor Azure apps service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Wanneer u service-eind punten voor Azure SQL Database gebruikt, is uitgaand naar Azure SQL Database open bare IP-adressen vereist: netwerk beveiligings groepen (Nsg's) moeten zijn geopend om te Azure SQL Database Ip's om verbinding te kunnen maken. U kunt dit doen met behulp van NSG-service tags voor Azure SQL Database.

Informatie over service tags met Service-eind punten voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Service Tags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: netwerk beveiligings configuraties voor uw Azure SQL database definiëren en implementeren met Azure Policy. U kunt de naam ruimte ' micro soft. SQL ' gebruiken om aangepaste beleids definities te definiëren of een van de ingebouwde beleids definities gebruiken die zijn ontworpen voor Server netwerk beveiliging. Een voor beeld van een toepasselijk ingebouwd netwerk beveiligings beleid voor een server is: "SQL Database moet een service-eind punt van een virtueel netwerk gebruiken.

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure resource management-sjablonen, Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en beleids regels, in één blauw definitie te verpakken. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (NSG) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw server. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt tijd bronnen voor Azure-resources. U kunt de tijd synchronisatie voor uw reken implementaties bijwerken.

Tijd synchronisatie voor Azure Compute-resources configureren:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel controle in voor Azure SQL database om database gebeurtenissen bij te houden en te schrijven naar een audit logboek in uw Azure Storage-Account, log Analytics werk ruimte of event hubs.

Daarnaast kunt u de telemetrie van Azure SQL Diagnostics streamen naar Azure SQL-analyse, een Cloud oplossing die de prestaties van Azure SQL Database en Azure SQL Managed instance op schaal en tussen meerdere abonnementen bewaakt. Het helpt u bij het verzamelen en visualiseren van Azure SQL Database prestatie gegevens en heeft ingebouwde intelligentie voor het oplossen van prestaties.

Controle instellen voor uw Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Platform logboeken en metrische gegevens verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Diagnostische gegevens streamen naar Azure SQL-analyse:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel controle in op uw server en kies een opslag locatie voor de audit Logboeken (Azure Storage, log Analytics of event hub).

Controle inschakelen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: wanneer u uw Azure SQL database-logboeken opslaat in een log Analytics-werk ruimte, stelt u de Bewaar periode voor het logboek in op basis van de nalevings regels van uw organisatie.

Para meters voor het bewaren van Logboeken instellen:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig resultaten bekijken. Gebruik de geavanceerde bedreigings beveiliging van Azure Security Center om te waarschuwen voor ongebruikelijke activiteiten die betrekking hebben op uw Azure SQL Database-exemplaar. U kunt ook waarschuwingen configureren op basis van metrische waarden of Azure-activiteiten logboek vermeldingen die betrekking hebben op uw Azure SQL Database exemplaren.

Meer informatie over geavanceerde beveiliging tegen bedreigingen en waarschuwingen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Aangepaste waarschuwingen configureren voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: gebruik Azure Security Center Advanced Threat Protection voor Azure SQL database voor bewaking en waarschuwingen over afwijkende activiteiten. Schakel Azure Defender voor SQL in voor uw SQL-data bases. Azure Defender voor SQL bevat functionaliteit voor halen en het beperken van potentiële database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base.

Meer informatie over geavanceerde beveiliging tegen bedreigingen en waarschuwingen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Azure Defender voor SQL inschakelen voor Azure SQL Database:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Waarschuwingen beheren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; voor Azure SQL Database wordt de anti-malware-oplossing beheerd door micro soft op het onderliggende platform.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; DNS-logboek registratie is niet van toepassing op Azure SQL Database.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; het controleren van de opdracht regel is niet van toepassing op Azure SQL Database.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad-hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Een directory-rol verkrijgen in azure AD met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directory-rol in azure AD ophalen met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory heeft niet het concept van standaard wachtwoorden. Bij het inrichten van een Azure SQL Database-exemplaar, kunt u het beste verificatie met Azure Active Directory integreren.

Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Meer informatie over Azure Security Center identiteit en toegang:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: niet van toepassing; Hoewel u Azure Active Directory-verificatie kunt configureren voor integratie met Azure SQL Database, wordt eenmalige aanmelding niet ondersteund.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met multi-factor Authentication MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

Meer informatie over privileged Access workstations:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Gebruik Advanced Threat Protection voor Azure SQL Database om afwijkende activiteiten te detecteren die ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Geavanceerde beveiliging tegen bedreigingen en mogelijke waarschuwingen bekijken:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: Gebruik voorwaardelijke toegang met de naam locaties om toegang tot portal-en Azure-resource beheer toe te staan vanaf alleen specifieke logische groepen met IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: maak een Azure Active Directory (Azure AD)-beheerder voor uw server.

Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Een Azure AD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van gebruikers kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Identity Access revisies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp** bij het configureren van Azure Active Directory-verificatie (Azure AD) met Azure SQL en het maken van diagnostische instellingen voor Azure Active Directory gebruikers accounts, het verzenden van de audit logboeken en aanmeldings logboeken naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren in Log Analytics werk ruimte.

Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Azure-activiteiten logboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) identiteits beveiliging en risico detecties om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. Daarnaast kunt u gegevens opnemen in azure Sentinel voor verder onderzoek.

Azure AD-risico aanmeldingen weer geven:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang heeft tot klant gegevens, biedt Azure klanten-lockbox een interface waarmee klanten aanvragen voor gegevens toegang van klanten kunnen controleren en goed keuren of afwijzen.

Klanten-lockbox begrijpen:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door Vnet/subnet, op de juiste wijze worden gelabeld en beveiligd in een NSG of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Persoonlijke koppeling gebruiken; Implementeer Azure SQL Database binnen uw Vnet en maak privé verbinding met behulp van privé-eind punten.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Persoonlijke koppeling instellen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor data bases in Azure SQL database het opslaan of verwerken van gevoelige informatie, markeert u de data base en gerelateerde resources als gevoelig voor het gebruik van tags. Configureer een persoonlijke koppeling in combi natie met de service tags voor de netwerk beveiligings groep op uw Azure SQL Database instanties om te voor komen dat gevoelige informatie wordt exfiltration.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Persoonlijke koppelingen en Nsg's configureren om te voor komen dat gegevens exfiltration op uw Azure SQL Database instanties:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Azure SQL database uw gegevens beveiligen door gegevens in beweging te versleutelen met Transport Layer Security. SQL Database dwingt te allen tijde versleuteling (SSL/TLS) af voor alle verbindingen. Dit zorgt ervoor dat alle gegevens in transit tussen de client en server worden versleuteld, ongeacht de instelling van versleutelen of TrustServerCertificate in de connection string.

Meer informatie over Azure SQL-versleuteling in transit:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: gebruik de functie voor Azure SQL database gegevens detectie en-classificatie. Gegevens detectie en-classificatie bieden geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen van &amp; het beveiligen van gevoelige gegevens in uw data bases.

Gegevens detectie en-classificatie gebruiken voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: gebruik Azure Active Directory (Azure AD) voor het verifiëren en beheren van toegang tot Azure SQL database-exemplaren.

Azure SQL Database integreren met Azure Active Directory voor verificatie:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Toegang beheren in Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: micro soft beheert de onderliggende infra structuur voor Azure SQL database en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: transparent Data Encryption (TDE) helpt bij het beschermen van Azure SQL database, Azure SQL Managed instance en Azure Data Warehouse tegen de dreiging van schadelijke offline activiteiten door het versleutelen van gegevens in rust. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. TDE is standaard ingeschakeld voor alle zojuist geïmplementeerde data bases in SQL Database en SQL Managed instance. De versleutelings sleutel TDE kan worden beheerd door micro soft of de klant.

Transparante gegevens versleuteling beheren en uw eigen versleutelings sleutels gebruiken:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure SQL database en andere essentiële of gerelateerde resources.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Azure Defender voor SQL inschakelen voor Azure SQL database en aanbevelingen volgen van Azure Security Center over het uitvoeren van beveiligings evaluaties op uw servers.

Beveiligings evaluaties uitvoeren op Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Azure Defender voor SQL inschakelen:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze bench Mark is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: periodieke terugkerende scans voor uw Azure SQL database-instanties inschakelen; Hiermee configureert u een evaluatie van beveiligings problemen om een keer per week automatisch een scan uit te voeren op uw data base. Er wordt een overzicht van de scan resultaten verzonden naar de e-mail adressen die u opgeeft. Vergelijk de resultaten om te controleren of de beveiligings problemen zijn opgelost.

Een evaluatie rapport voor beveiligings problemen exporteren in Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

Azure Security Center beveiligde Score begrijpen:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (inclusief Azure SQL database) binnen uw abonnement (en) te doorzoeken en te detecteren.  Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Meer informatie over Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Hulp**: een lijst met goedgekeurde Azure-resources en goedgekeurde software voor uw reken resources definiëren

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen uw abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen te plaatsen voor het type resources dat kan worden gemaakt in klant abonnementen met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen uw abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor toepassingen die worden uitgevoerd op reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor App Service-of COMPUTE-resources die fungeren als host voor desktop-of webtoepassingen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy of Azure Security Center aanbevelingen voor Azure SQL database voor het onderhouden van beveiligings configuraties voor alle Azure-resources.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. SQL ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: Maak gebruik van Azure Security Center om basislijn scans uit te voeren voor Azure SQL database.

Aanbevelingen herstellen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Azure Key Vault om versleutelings sleutels voor Azure SQL database transparent Data Encryption op te slaan (TDE).

Het beveiligen van gevoelige gegevens die worden opgeslagen in Azure SQL Database en het opslaan van de versleutelings sleutels in Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Key Vault, zonder enige referenties in uw code.

Zelf studie: een door Windows-VM-systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Beheerde identiteiten configureren:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in uw code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure app service), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure SQL Database, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

Meer informatie over micro soft antimalware voor Azure Cloud Services en Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: om uw bedrijf te beschermen tegen gegevens verlies, maakt Azure SQL database automatisch volledige back-ups van de data base, differentiële database back-ups elke 12 uur en back-ups van transactie Logboeken om de 5-10 minuten. De back-ups worden opgeslagen in RA-GRS-opslag gedurende ten minste 7 dagen voor alle service lagen. Alle service lagen, met uitzonde ring van Basic Support Configureer bare Bewaar periode voor back-ups voor herstel naar een bepaald tijdstip, Maxi maal 35 dagen.

Als u wilt voldoen aan verschillende nalevings vereisten, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslag verbruik is afhankelijk van de geselecteerde frequentie van back-ups en de retentie periode (n).

Meer informatie over back-ups en bedrijfs continuïteit met Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: Azure SQL database maakt automatisch de database back-ups die tussen 7 en 35 dagen worden bewaard en maakt gebruik van Azure-geo-redundante opslag met lees toegang (RA-GRS) om ervoor te zorgen dat ze worden bewaard, zelfs als het Data Center niet beschikbaar is. Deze back-ups worden automatisch gemaakt. Schakel zo nodig geo-redundante back-ups op lange termijn in voor uw Azure SQL-data bases.

Als door de klant beheerde sleutels worden gebruikt voor Transparent Data Encryption, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

Meer informatie over back-ups in Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Hoe kan ik een back-up maken van sleutel kluis sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat het gegevens herstel van inhoud regel matig wordt uitgevoerd in azure backup. Test zo nodig inhoud terug naar een geïsoleerd VLAN. Het herstellen van een back-up van door de klant beheerde sleutels testen.

Sleutel kluis sleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Azure SQL Database back-ups herstellen met behulp van herstel naar een bepaald tijdstip:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel zacht verwijderen in azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Het inschakelen van zacht verwijderen in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richt lijnen**: Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die rollen van personeel en fasen van incident handling/Management definiëren.

Werk stroom automatisering configureren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Beveiligings waarschuwingen in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

U kunt naar de publicatie van het NIST verwijzen: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Sentinel te verzenden.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen, hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../../security/benchmarks/security-baselines-overview.md)