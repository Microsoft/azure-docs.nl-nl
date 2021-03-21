---
title: Azure-beveiligings basislijn voor Azure Web Application firewall
description: De beveiligings basislijn van de firewall van Azure Web Application biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211631"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure-beveiligings basislijn voor Azure Web Application firewall

Deze beveiligings basislijn is van toepassing op de richt lijnen van [Azure Security Bench Mark versie 1,0](../security/benchmarks/overview-v1.md) naar Azure Web Application firewall. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op de firewall van Azure Web Application. **Besturings elementen** die niet van toepassing zijn op de firewall van Azure Web Application, zijn uitgesloten. 

Zie voor meer informatie over hoe de firewall van Azure-webtoepassingen volledig is toegewezen aan de beveiligings benchmark van Azure [Web Application firewall](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Hulp**: gebruik Microsoft Azure Web Application firewall (WAF) voor gecentraliseerde beveiliging van webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen zoals SQL-injectie en cross-site scripting. 

- Detectie modus: gebruik deze modus voor het leren van het netwerk verkeer, inzicht te krijgen en fout-positieven te controleren. Hiermee worden alle bedreigings waarschuwingen gecontroleerd en geregistreerd. Zorg ervoor dat diagnostische gegevens en WAF-logboek zijn geselecteerd en ingeschakeld. Houd er rekening mee dat binnenkomende aanvragen niet worden geblokkeerd door de WAF wanneer deze wordt uitgevoerd in de detectie modus.
- Preventie modus: blokkeert indringingen en aanvallen gedetecteerd door de regels. Een aanvaller ontvangt een uitzonde ring "403 niet-geautoriseerde toegang" en de verbinding is gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

Basis lijn voor het netwerk verkeer met de detectie modus van het WAF. Nadat u uw verkeers behoeften hebt bepaald, configureert u de WAF in de modus preventie om ongewenst verkeer te Bock.

Volg de aanbevelingen op het niveau van Security Center voor alle bronnen die niet worden beveiligd door WAF.  

- [CRS-regel groepen en-regels voor Web Application firewall](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF modi op Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF modi aan de voor deur](afds/afds-overview.md#waf-modes)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: gebruik aangepaste regels met de Azure Web Application firewall (WAF) om verkeer toe te staan of te blok keren. Zo kan al het verkeer dat afkomstig is van een bereik van IP-adressen, worden geblokkeerd. Configureer Azure WAF om uit te voeren in de modus voor preventie, waardoor indringingen en aanvallen die door de regels worden gedetecteerd, worden geblokkeerd. Een aanvaller ontvangt een uitzonde ring "403 niet-geautoriseerde toegang" en de verbinding is gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [WAF modi op Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF modi aan de voor deur](afds/afds-overview.md#waf-modes)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Hulp**: Azure Web Application firewall (WAF) is een kern onderdeel van de beveiliging van de Web-App van Azure. Gebruik Azure WAF om gecentraliseerde beveiliging te bieden voor webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen met vooraf geconfigureerde, beheerde regel voor bekende aanvals handtekeningen van OWASP TOP 10-categorieën.

Pas Azure WAF aan met regels en regel groepen om te voldoen aan de vereisten van webtoepassingen en voorkom valse positieven. Koppel een Azure WAF-beleid voor elke site achter een WAF om site-specifieke configuratie toe te staan. Azure WAF ondersteunt regels voor geo-filtering, tarieven beperken, door Azure beheerde standaardregel sets. aangepaste regels kunnen worden gemaakt op basis van de behoeften van een toepassing. 

Configureer de Azure-WAF om uit te voeren in preventie modus nadat het netwerk verkeer in de detectie modus gedurende een bepaalde periode is basis lijnen. De Azure-WAF blokkeert inbreuken en aanvallen die zijn gedetecteerd door de regels in de modus preventie. Een aanvaller ontvangt een uitzonde ring "403 niet-geautoriseerde toegang" en de verbinding is gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [WAF modi op Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF modi aan de voor deur](afds/afds-overview.md#waf-modes)

- [CRS-regel groepen en-regels voor Web Application firewall](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: resources maken, koppelen en logisch organiseren in een Azure-abonnement met tags voor de detectie van veelvoorkomende onjuiste configuraties van toepassingen (bijvoorbeeld Apache en IIS). 

Regels en regel groepen Toep assen op Azure Web Application firewall-beleid (WAF) op basis van de toegepaste meta gegevens van het label.

- [WAF-beleid voor Application Gateway](/cli/azure/network/application-gateway/waf-policy) 

- [WAF-beleid op de voor deur](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen die zijn gekoppeld aan de Azure Web Application firewall (WAF) in uw Azure-toepassing gateway-subnet en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik voor afzonderlijke regels voor de netwerk beveiligings groep het veld Beschrijving om de bedrijfs behoeften, duur, enzovoort op te geven voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

Kies Azure PowerShell of Azure CLI om op basis van hun labels acties op resources te zoeken of uit te voeren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk bron configuraties te bewaken en wijzigingen te detecteren voor netwerk instellingen en-bronnen die betrekking hebben op uw Azure Web Application firewall (WAF)-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer wijzigingen in essentiële netwerk instellingen of bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: Maak een netwerk regel voor Azure Web Application firewall (WAF) om toegang tot een NTP-server toe te staan met de juiste poort en protocol, zoals poort 123 via UDP.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp** bij het configureren van Azure Web Application firewall-Logboeken (WAF) die moeten worden verzonden naar een beheer oplossing voor centraal beveiligings logboeken, zoals Azure Sentinel, of een Siem van derden. Deze logboeken bevatten Azure-activiteiten, diagnostische gegevens en real-time WAF-Logboeken. deze logboeken kunnen vervolgens worden weer gegeven in verschillende hulpprogram ma's, zoals Azure Monitor, Excel en Power BI. De logboeken van de firewall van Azure Web Application geven inzicht in de gegevens die door de Azure-WAF worden geëvalueerd, vergeleken en geblokkeerd.

Azure Sentinel heeft een ingebouwde Azure WAF-werkmap, die een overzicht geeft van de beveiligings gebeurtenissen op de Azure-WAF. Deze werkmap bevat gebeurtenissen, overeenkomsten die overeenkomen met en geblokkeerde regels en alles wat er wordt geregistreerd in de firewall Logboeken. Deze telemetrie kan worden gebruikt om Playbook Automation uit te voeren om acties op basis van WAF-gebeurtenissen die door Azure Sentinel worden verzameld, op de hoogte te stellen of op te lossen.

- [Activiteiten logboeken weer geven](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnostische logboeken voor Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Gegevens verbinden van micro soft Web Application Firewall naar Azure Sentinel](../sentinel/connect-azure-waf.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel logboek registratie in op uw Azure Web Application firewall-bronnen (WAF) voor toegang tot controle-, beveiligings-en Diagnostische logboeken. Azure Web Application Firewall biedt gedetailleerde rapportage over alle gedetecteerde bedreigingen die beschikbaar worden gesteld in de geconfigureerde Diagnostische logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

- [Logboek registratie-overzicht](ag/ag-overview.md#logging)

- [Overzicht van Azure Monitor-logboek query](../azure-monitor/logs/log-query-overview.md)

- [Overzicht van Azure platform-logboeken](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: verzend de Azure Web Application firewall-Logboeken (WAF) naar een aangepast opslag account en definieer het Bewaar beleid. Gebruik Azure Monitor om de Bewaar periode van uw Log Analytics werk ruimte in te stellen op basis van de nalevings vereisten van uw organisatie.
- [Bewaking configureren voor een opslag account](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Azure Web Application firewall (WAF) biedt gedetailleerde rapportage over elke gedetecteerde bedreiging. Logboek registratie is geïntegreerd met Azure Diagnostics logboeken met uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. 

Azure WAF-exemplaren zijn geïntegreerd met Security Center voor het verzenden van waarschuwingen en status informatie voor rapportage. Gebruik aanbevelingen van Security Center om onbeveiligde webtoepassingen te detecteren en deze kwets bare bronnen te beveiligen. 

Azure Sentinel heeft een ingebouwde WAF-firewall, die een overzicht geeft van de beveiligings gebeurtenissen op de WAF. Dit zijn onder andere gebeurtenissen, overeenkomende en geblokkeerde regels, en alles wat u kunt vastleggen in de logboeken van de firewall.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/index.yml) 

- [Diagnostische instellingen inschakelen voor Azure-toepassing gateway](../application-gateway/application-gateway-diagnostics.md)

- [Metrische gegevens en logboeken bewaken in azure front deur](../frontdoor/front-door-diagnostics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure-WAF en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. Waarschuwingen maken voor afwijkende activiteiten op basis van metrische gegevens over WAF. Als er bijvoorbeeld een geblokkeerd aantal aanvragen van meer dan X, ' Y ' is.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Waarschuwingen maken in azure](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: implementeer Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. 

Azure WAF biedt gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen en beveiligt uw apps door inkomend webverkeer te inspecteren om aanvallen zoals SQL-injecties, cross-site scripting, malware-uploads en DDoS-aanvallen te blok keren.

- [Azure WAF implementeren](ag/create-waf-policy-ag.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die query's kunnen uitvoeren en expliciet moeten worden toegewezen. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: er zijn geen lokale beheerders toewijzingen beschikbaar in het WAF. Er kunnen echter Azure Active Directory (Azure AD) beheerders rollen in de omgeving zijn die beheer mogelijkheden kunnen bieden voor de Azure WAF-resources.
Het is een goed idee om standaard procedures te maken voor het gebruik van speciale beheerders accounts die toegang hebben tot WAF-exemplaren (Azure Web Application firewall). Gebruik de functies voor identiteits-en toegangs beheer van Security Center om het aantal beheerders accounts te controleren.

- [Inzicht in Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Meer informatie over het maken van gebruikers met beheerders rechten in Azure Database for PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication (MFA) in en volg de aanbevelingen voor identiteits-en toegangs beheer van Security Center.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access Workstation) met multi-factor Authentication (MFA) die zijn geconfigureerd voor aanmelding bij en configureer Azure Web Application firewall (WAF) en gerelateerde bronnen. 

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD)-beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: de locatie voorwaarde van een beleid voor voorwaardelijke toegang configureren en uw benoemde locaties beheren. 

Maak logische groeperingen van IP-adresbereiken of landen en regio's met benoemde locaties. Beperk de toegang tot uw gevoelige bronnen, zoals Azure Key Vault geheimen, tot uw geconfigureerde benoemde locaties.

- [Wat is de voor waarde voor de locatie in Azure Active Directory voorwaardelijke toegang](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties.
- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Gebruik beoordelingen van Azure Identity Access om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Controleer de gebruikers toegang op regel matige basis om ervoor te zorgen dat alleen actieve gebruikers de toegang blijven houden.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Integreer Azure Active Directory (Azure AD) aanmeldings activiteiten, controle en risico gebeurtenis logboek bronnen, met elk Siem-of bewakings programma zoals Azure Sentinel.

Stroom lijn dit proces door Diagnostische instellingen te maken voor gebruikers accounts voor Azure Active Directory (Azure AD) en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in de Log Analytics-werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik de functies voor risico-en identiteits beveiliging van Azure Active Directory (Azure AD) om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. Gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het bijhouden van Azure Web Application firewall (WAF) en gerelateerde resources die gevoelige informatie opslaan of verwerken.
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau, bijvoorbeeld ontwikkelings-, test-en productie omgevingen. 

Toegang tot Azure-resources beheren met Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw WAF-exemplaren (Azure Web Application firewall) en gerelateerde resources, kunnen onderhandelen over TLS 1,2 of hoger.

Volg Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in transit, indien van toepassing.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: toegang tot Azure-resources beheren met Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: Gebruik versleuteling in rust voor alle Azure-resources, waaronder Azure Web Application firewall (WAF) en gerelateerde bronnen. Micro soft raadt u aan Azure te laten beheren van uw versleutelings sleutels, maar in sommige gevallen kunt u ook uw eigen sleutels beheren.

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

- [Door de klant beheerde versleutelings sleutels configureren](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Richt lijnen**: Configureer Azure Web Application firewall (WAF) om uit te voeren in preventie modus nadat het netwerk verkeer in de detectie modus voor een vooraf bepaalde tijd is basis lijnen. 

De Azure-WAF, in preventie modus, blokkeert inbreuken en aanvallen die worden gedetecteerd door de regels. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [Overzicht van de integratie tussen Application Gateway en Azure Security Center](../security-center/security-center-partner-integration.md)

- [WAF modi op Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF modi aan de voor deur](afds/afds-overview.md#waf-modes)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources te doorzoeken of te detecteren, zoals compute, opslag, netwerk, poorten en protocollen, enzovoort binnen uw abonnementen. 

Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en de resources in uw abonnementen inventariseren. Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels gebruiken in azure Web Application firewall-beleid (WAF). Labels kunnen worden gekoppeld aan resources en logisch worden toegepast om de toegang tot deze resources in een klant abonnement te organiseren. 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure WAF en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: een inventaris van goedgekeurde resources maken, inclusief configuratie op basis van de behoeften van de organisatie.

Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. Zorg ervoor dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.
Gebruik Azure resource Graph voor het opvragen of detecteren van Azure Web Application firewall-resources (WAF) binnen hun abonnementen. Zorg ervoor dat alle Azure-WAF en gerelateerde resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet-goedgekeurde Azure WAF-resources bewaken en verwijderen met Azure Policy om de implementatie van Azure WAF of een bepaald type WAF te weigeren, bijvoorbeeld Azure WAF v1 VS v2.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheid van een gebruiker om te communiceren met Azure-Resources Manager te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: Azure Web Application firewall (WAF) kan worden gekoppeld aan verschillende omgevingen via netwerken, resource groepen of abonnementen om toepassingen met een hoog risico te scheiden.

- [Overzicht van Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)

- [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen met betrekking tot uw WAF-implementaties (Azure Web Application firewall).
Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van uw Azure-toepassing gateways, virtuele netwerken, netwerk beveiligings groepen en het gebruik van ingebouwde beleids definities.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure Web Application firewall (WAF) en gerelateerde bronnen. 

Gebruik Azure Resource Manager sjablonen om de beveiligings configuratie van uw Azure-WAF en gerelateerde resources die uw organisatie vereist, te hand haven.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels en Azure Resource Manager sjablonen. 

Machtigingen verlenen of weigeren voor specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD), indien geïntegreerd met Azure DevOps of Active Directory, indien geïntegreerd met Team Foundation Server (TFS).

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Network ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Documentatie voor Azure Policy](../governance/policy/index.yml)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Network ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. 

Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] effecten om automatisch configuraties voor uw Azure-resources af te dwingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Documentatie voor Azure Policy](../governance/policy/index.yml)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Azure Key Vault om certificaten veilig op te slaan. Azure Key Vault is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en SSL-certificaten. 

Azure-toepassing gateway ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit. 

- [SSL-beëindiging met Key Vault certificaten configureren met behulp van Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Hulp** bij het implementeren van referentie scanner om referenties in code te identificeren, waardoor het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault, wordt aangeraden.
- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Zorg ervoor dat de functie voor voorlopig verwijderen is ingeschakeld voor Azure Key Vault. Met zacht verwijderen kunt u verwijderde sleutel kluizen en kluis objecten zoals sleutels, geheimen en certificaten herstellen.

- [De tijdelijke verwijdering van Azure Key Vault gebruiken](../key-vault/general/key-vault-recovery.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident. 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.
Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.
- [Raadpleeg de publicatie handleiding van het NIST om Program Ma's te testen, te trainen en uit te oefenen voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.
- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. Gebruik de Azure Security Center Data Connector om de waarschuwingen naar Azure Sentinel te streamen volgens de vereisten van uw organisatie.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.
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