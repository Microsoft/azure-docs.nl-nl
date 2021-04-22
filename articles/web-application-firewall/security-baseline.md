---
title: Azure-beveiligingsbasislijn voor Azure Web Application Firewall
description: De Azure Web Application Firewall beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875954"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure-beveiligingsbasislijn voor Azure Web Application Firewall

Met deze beveiligingsbasislijn worden richtlijnen uit [azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Azure Web Application Firewall. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van **de** beveiligingscontroles die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Web Application Firewall. 

> [!NOTE]
> **Besturingselementen** die niet van Azure Web Application Firewall zijn of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten. Zie het volledige Azure Web Application Firewall toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Web Application Firewall volledig is toegewezen aan de Azure **[Azure Web Application Firewall Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** gebruik Microsoft Azure Web Application Firewall (WAF) voor gecentraliseerde beveiliging van webtoepassingen tegen veelvoorkomende aanvallen en beveiligingsproblemen, zoals SQL-injectie en cross-site scripting. 

- Detectiemodus: gebruik deze modus om het netwerkverkeer te leren, fout-positieven te begrijpen en te beoordelen. Het bewaakt en registreert alle bedreigingswaarschuwingen. Zorg ervoor dat Diagnostische gegevens en WAF-logboek zijn geselecteerd en ingeschakeld. Houd er rekening mee dat de WAF binnenkomende aanvragen niet blokkeert wanneer deze wordt gebruikt in de detectiemodus.
- Preventiemodus: blokkeert indringers en aanvallen die door de regels zijn gedetecteerd. Een aanvaller ontvangt een uitzondering '403 onbevoegde toegang' en de verbinding wordt gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

Basislijn voor uw netwerkverkeer met de detectiemodus van de WAF. Nadat u de behoeften voor verkeer hebt bepaald, configureert u de WAF in de preventiemodus voor bock-ongewenst verkeer.

Volg aanbevelingen met hoge urgentie van Security Center voor webbronnen die niet worden beveiligd door WAF.  

- [Web Application Firewall CRS-regelgroepen en -regels](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF-modi op Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-modi op Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** gebruik aangepaste regels met de Azure Web Application Firewall (WAF) om verkeer toe te staan en te blokkeren. Al het verkeer dat afkomstig is van een bereik van IP-adressen kan bijvoorbeeld worden geblokkeerd. Configureer Azure WAF om uit te voeren in de preventiemodus, waardoor indringers en aanvallen die door de regels worden gedetecteerd, worden blokkeert. Een aanvaller ontvangt de uitzondering '403 onbevoegde toegang' en de verbinding wordt gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [WAF-modi op Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-modi op Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Azure Web Application Firewall (WAF) is het belangrijkste onderdeel van de azure-webtoepassingsbeveiliging. Gebruik Azure WAF om gecentraliseerde beveiliging te bieden voor webtoepassingen tegen veelvoorkomende aanvallen en beveiligingsproblemen met vooraf geconfigureerde beheerde regelset tegen bekende aanvalshandtekeningen uit OWASP TOP 10-categorieën.

Pas Azure WAF aan met regels en regelgroepen om webtoepassingsvereisten af te sluiten en fout-positieven te elimineren. Koppel een Azure WAF-beleid voor elke site achter een WAF om sitespecifieke configuratie toe te staan. Azure WAF ondersteunt regels voor geofilters, snelheidsbeperking en door Azure beheerde standaardregelset. en aangepaste regels kunnen worden gemaakt om te voldoen aan de behoeften van een toepassing. 

Configureer de Azure WAF om te worden uitgevoerd in de preventiemodus nadat u het netwerkverkeer in de detectiemodus voor een bepaalde periode hebt ge baselijnd. De Azure WAF blokkeert indringers en aanvallen die zijn gedetecteerd door de regels in de preventiemodus. Een aanvaller ontvangt een uitzondering '403 onbevoegde toegang' en de verbinding wordt gesloten. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [WAF-modi op Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-modi op Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [CRS-regelgroepen en -regels voor Web Application Firewall](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** Resources maken, koppelen en logisch organiseren in een Azure-abonnement met tags voor het detecteren van veelvoorkomende onjuiste configuraties van toepassingen (bijvoorbeeld Apache en IIS). 

Regels en regelgroepen toepassen op Azure Web Application Firewall beleidsregels (WAF) op basis van de toegepaste metagegevens van de tag.

- [WAF-beleid voor Application Gateway](/cli/azure/network/application-gateway/waf-policy)

- [WAF-beleid voor Front Door](/cli/azure/network/front-door/waf-policy)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** gebruik tags voor netwerkbeveiligingsgroepen die zijn gekoppeld aan de Azure Web Application Firewall (WAF) in uw Azure Application Gateway-subnet, evenals andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Gebruik voor afzonderlijke regels voor netwerkbeveiligingsgroep het veld Beschrijving om de bedrijfse behoeften, duur, en meer op te geven voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag vereisen en de waarde ervan, om ervoor te zorgen dat alle resources worden gemaakt met tags en u op de hoogte te stellen van bestaande resources zonder tag.

Kies Azure PowerShell of Azure CLI om op resources te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren voor netwerkinstellingen en resources met betrekking tot uw Azure Web Application Firewall (WAF)-implementaties. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkinstellingen of resources plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Goedgekeurde tijdsynchronisatiebronnen gebruiken

**Richtlijnen:** maak een netwerkregel voor Azure Web Application Firewall (WAF) om toegang tot een NTP-server met de juiste poort en het juiste protocol toe te staan, zoals poort 123 via UDP.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** configureer Azure Web Application Firewall logboeken (WAF) die moeten worden verzonden naar een centrale oplossing voor het beheer van beveiligingslogboeken, zoals Azure Sentinel, of een SIEM van derden. Deze logboeken bevatten Azure-activiteiten, diagnostische logboeken en realtime WAF-logboeken. Deze logboeken kunnen vervolgens worden bekeken in verschillende hulpprogramma's, zoals Azure Monitor, Excel en Power BI. Azure Web Application Firewall logboeken geven inzicht in welke gegevens de Azure WAF evalueert, overeenkomt en blokkeert.

Azure Sentinel beschikt over een ingebouwde Azure WAF-werkmap, die een overzicht biedt van de beveiligingsgebeurtenissen op de Azure WAF. Deze werkmap bevat gebeurtenissen, overeenkomende en geblokkeerde regels en alle andere regels die in de firewalllogboeken worden vastgelegd. Deze telemetrie kan worden gebruikt om playbookautomatisering te laten plaatsvinden of herstelacties uit te voeren op basis van WAF-gebeurtenissen die zijn verzameld door Azure Sentinel.

- [Activiteitenlogboeken weergeven](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnostische logboeken voor Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Gegevens van Microsoft Web Application Firewall verbinden met Azure Sentinel](../sentinel/connect-azure-waf.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Logboekregistratie inschakelen op uw Azure Web Application Firewall (WAF)-resources voor toegang tot audit-, beveiligings- en diagnostische logboeken. Azure Web Application Firewall biedt gedetailleerde rapportage over elk van de gedetecteerde bedreigingen die beschikbaar worden gesteld in de geconfigureerde diagnostische logboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, doeladressen en andere nuttige elementen.

- [Overzicht van logboekregistratie](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Monitor van logboekquery's](../azure-monitor/logs/log-query-overview.md)

- [Overzicht van Azure Platform-logboeken](../azure-monitor/essentials/platform-logs-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** verzend de Azure Web Application Firewall logboeken (WAF) naar een aangepast opslagaccount en definieer het bewaarbeleid. Gebruik Azure Monitor om de retentieperiode van uw Log Analytics-werkruimte in te stellen op basis van de nalevingsvereisten van uw organisatie.
- [Bewaking configureren voor een opslagaccount](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Azure Web Application Firewall (WAF) biedt gedetailleerde rapportage over elke gedetecteerde bedreiging. Logboekregistratie is geïntegreerd met Azure Diagnostics logboeken die uitgebreide informatie bieden over bewerkingen en fouten die belangrijk zijn voor controle en probleemoplossing. 

Azure WAF-exemplaren zijn geïntegreerd met Security Center waarschuwingen en statusinformatie voor rapportage te verzenden. Gebruik Security Center van de Security Center om onbeveiligde webtoepassingen te detecteren en deze kwetsbare resources te beveiligen. 

Azure Sentinel beschikt over een ingebouwde werkmap WAF- firewallgebeurtenissen, die een overzicht biedt van de beveiligingsgebeurtenissen op de WAF. Dit zijn gebeurtenissen, overeenkomende en geblokkeerde regels en alle andere regels die in de firewalllogboeken worden vastgelegd.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](/azure/azure-monitor/platform/activity-log)

- [Diagnostische instellingen inschakelen voor Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Bewaking van metrische gegevens en logboeken in Azure Front Door](../frontdoor/front-door-diagnostics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken in, evenals de diagnostische instellingen voor uw Azure WAF, en verzend de logboeken naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. Maak waarschuwingen voor afwijkende activiteiten op basis van metrische WAF-gegevens. Als bijvoorbeeld het aantal aanvragen is geblokkeerd dat 'X' overschrijdt, doet u 'Y'.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Waarschuwingen maken in Azure](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** Implementeer Azure Web Application Firewall (WAF) vóór kritieke webtoepassingen voor aanvullende inspectie van binnenkomend verkeer. 

Azure WAF biedt gecentraliseerde beveiliging van uw webtoepassingen tegen veelvoorkomende aanvallen en beveiligingsproblemen en beveiligt uw apps door inkomende webverkeer te inspecteren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blokkeren.

- [Azure WAF implementeren](ag/create-waf-policy-ag.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) beschikt over ingebouwde rollen, kunnen query's uitvoeren en moeten expliciet worden toegewezen. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** er zijn geen lokale beheerderstoewijzingen beschikbaar in de WAF. Er kunnen echter beheerdersrollen Azure Active Directory (Azure AD) in de omgeving zijn die beheerbeheer over de Azure WAF-resources mogelijk maken.
Het is een goede gewoonte om standaardprocedures te maken voor het gebruik van toegewezen beheerdersaccounts die toegang hebben tot Azure Web Application Firewall (WAF)-exemplaren. Gebruik Security Center's Identity and Access Management om het aantal beheerdersaccounts te bewaken.

- [Inzicht Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Meer inzicht in het maken van beheerdersgebruikers in Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Hoe u Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en de aanbevelingen Security Center identiteits- en toegangsbeheer van uw bedrijf volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Paw (Privileged Access Workstation) gebruiken met meervoudige verificatie geconfigureerd voor het aanmelden bij en configureren van Azure Web Application Firewall (WAF) en gerelateerde resources.

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** Configureer de locatievoorwaarde van een beleid voor voorwaardelijke toegang en beheer uw benoemde locaties.

Maak logische groeperingen van IP-adresbereiken of landen en regio's met benoemde locaties. Beperk de toegang tot uw gevoelige resources, zoals Azure Key Vault, tot uw geconfigureerde benoemde locaties.

- [Wat is de locatievoorwaarde in Azure Active Directory (Azure AD) Voorwaardelijke toegang](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beveiligt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit en ook salts, hashes en slaat gebruikersreferenties veilig op.
- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Controleer regelmatig de gebruikerstoegang om ervoor te zorgen dat alleen actieve gebruikers toegang blijven hebben.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring)

- [Azure Identity Access Reviews gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Integreer Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen met elk SIEM- of bewakingshulpprogramma zoals Azure Sentinel.

Stroomlijn dit proces door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. Configureer de gewenste waarschuwingen in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik de functies Azure Active Directory (Azure AD) Risk and Identity Protection (Azure AD) voor het configureren van geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om te helpen bij het bijhouden Azure Web Application Firewall (WAF) en gerelateerde resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Isolatie implementeren met behulp van afzonderlijke abonnementen en beheergroepen voor afzonderlijke beveiligingsdomeinen, zoals omgevingstype en gevoeligheidsniveau voor gegevens, bijvoorbeeld ontwikkel-, test- en productieomgevingen. 

Toegang tot Azure-resources beheren met Azure Active Directory (Azure AD) op rollen gebaseerd toegangsbeheer (Azure RBAC).

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Versleutel alle gevoelige gegevens die onderweg zijn. Zorg ervoor dat clients die verbinding maken met uw Azure Web Application Firewall(WAF)-exemplaren en gerelateerde resources, kunnen onderhandelen over TLS 1.2 of hoger.

Volg Security Center aanbevelingen voor versleuteling in rust en versleuteling in transit, indien van toepassing.

- [Inzicht in versleuteling tijdens overdracht met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richtlijnen:** Toegang tot uw Azure-resources beheren, Web Application Firewall met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).

- [Azure RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Versleuteling-at-rest gebruiken voor alle Azure-resources, Azure Web Application Firewall (WAF) en gerelateerde resources. Microsoft raadt u aan om Azure toe te staan uw versleutelingssleutels te beheren, maar in sommige gevallen kunt u uw eigen sleutels beheren.

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

- [Door de klant beheerde versleutelingssleutels configureren](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** configureer Azure Web Application Firewall (WAF) om te worden uitgevoerd in de preventiemodus nadat het netwerkverkeer in de detectiemodus voor een vooraf bepaalde hoeveelheid tijd is gebasislijnd. 

De Azure WAF blokkeert in de preventiemodus indringers en aanvallen die door de regels worden gedetecteerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

- [Overzicht van de integratie tussen Application Gateway en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [WAF-modi op Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-modi op Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen of ontdekken van alle resources, zoals rekenkracht, opslag, netwerk, poorten en protocollen, en dergelijke binnen uw abonnementen.

Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en de resources binnen uw abonnementen. Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags gebruiken op Azure Web Application Firewall (WAF)-beleid. Tags kunnen worden gekoppeld aan resources en logisch worden toegepast om de toegang tot deze resources in een klantabonnement te organiseren. 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om Azure WAF en gerelateerde resources te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit het abonnement op tijd.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventarisatie van goedgekeurde resources, inclusief configuratie op basis van de behoeften van de organisatie.

Gebruik Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt.
Gebruik Azure Resource Graph om query's uit te voeren op Azure Web Application Firewall (WAF)-resources binnen hun abonnementen. Zorg ervoor dat alle Azure WAF en gerelateerde resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** niet-goedgekeurde Azure WAF-resources bewaken en verwijderen met Azure Policy om de implementatie van Azure WAF of een bepaald type WAF te weigeren, bijvoorbeeld Azure WAF v1 versus V2.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik voorwaardelijke toegang van Azure om de mogelijkheid van een gebruiker om te communiceren met Azure Resources Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Azure Web Application Firewall (WAF) kunnen worden gekoppeld aan verschillende omgevingen via netwerken, resourcegroepen of abonnementen om toepassingen met een hoog risico te scheiden.

- [Overzicht van Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)

- [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkinstellingen die betrekking hebben op Azure Web Application Firewall implementaties (WAF).

Gebruik Azure Policy-aliassen in de naamruimte Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-toepassing-gateways, virtuele netwerken en netwerkbeveiligingsgroepen te controleren of af te dwingen en om ingebouwde beleidsdefinities te gebruiken.

- [Beschikbare aliassen Azure Policy weergeven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [deny] en [deploy if not exist] effecten om veilige instellingen af te dwingen voor uw Azure Web Application Firewall (WAF) en gerelateerde resources. 

Gebruik Azure Resource Manager voor het onderhouden van de beveiligingsconfiguratie van uw Azure WAF en gerelateerde resources die uw organisatie nodig heeft.

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels en Azure Resource Manager sjablonen.

Verleen of weiger machtigingen aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD), indien geïntegreerd met Azure DevOps of Azure AD, indien geïntegreerd met Team Foundation Server (TFS).

- [Code opslaan in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Network om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Ontwikkel een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Documentatie voor Azure Policy](/azure/governance/policy)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Network om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. 

Gebruik Azure Policy effecten [audit], [deny] en [deploy if not exist] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Documentatie voor Azure Policy](/azure/governance/policy)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** gebruik Azure Key Vault om certificaten veilig op te slaan. Azure Key Vault is een door het platform beheerd geheim opslag dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en SSL-certificaten. 

Azure Application Gateway ondersteunt integratie met Key Vault voor servercertificaten die zijn gekoppeld aan listeners met HTTPS-ondersteuning. 

- [SSL-beëindiging configureren met Key Vault certificaten met behulp van Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Implementeert referentiescanner om referenties in code te identificeren. Hierdoor wordt ook het verplaatsen van gevonden referenties naar veiligere locaties, zoals Azure Key Vault.
- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** zorg ervoor dat de functie voor het verwijderen van Azure Key Vault. Met de functie voor soft delete kunnen verwijderde sleutelkluizen en kluisobjecten, zoals sleutels, geheimen en certificaten, worden hersteld.

- [De soft delete van Azure Key Vault van uw account gebruiken](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Ontwikkel een handleiding voor het reageren op incidenten voor uw organisatie. Zorg ervoor dat er geschreven plannen voor incidentrespons zijn waarin alle rollen van medewerkers worden bepaald, evenals de fasen van incidentafhandeling en -beheer, van detectie tot incidentbeoordeling. 

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Gebruik de Computer Security Incident Handling Guide van NIST om u te helpen bij het maken van uw eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in het vinden of de metrische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor incidentrespons van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.
- [Raadpleeg de publicatiehandleiding van NIST voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.
- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Security Center en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. Gebruik de Azure Security Center-gegevensconnector om de waarschuwingen te Azure Sentinel volgens de vereisten van uw organisatie.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.
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
