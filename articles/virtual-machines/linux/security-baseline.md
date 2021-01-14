---
title: Azure-beveiligings basislijn voor Linux Virtual Machines
description: De Linux Virtual Machines Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f0ae4ac4cf50eb6e54d41ff25e1cc671e76819ee
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200801"
---
# <a name="azure-security-baseline-for-linux-virtual-machines"></a>Azure-beveiligings basislijn voor Linux Virtual Machines

De Azure-beveiligings basislijn voor Linux Virtual Machines bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: wanneer u een virtuele Azure-machine (VM) maakt, moet u een virtueel netwerk (VNet) maken of een bestaand vnet gebruiken en de virtuele machine met een subnet configureren. Zorg ervoor dat voor alle geïmplementeerde subnetten een netwerk beveiligings groep is toegepast met netwerk toegangs beheer die specifiek is voor uw toepassingen vertrouwde poorten en bronnen.

Als u een specifieke use-case voor een gecentraliseerde firewall hebt, kunt Azure Firewall ook worden gebruikt om aan deze vereisten te voldoen.

* [Virtuele netwerken en virtuele machines in azure](../network-overview.md)

* [Een Virtual Network maken](../../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall implementeren en configureren](../../firewall/tutorial-firewall-deploy-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik de Azure Security Center voor het identificeren en volgen van aanbevelingen voor netwerk beveiliging voor het beveiligen van uw Azure virtual machine-resources (VM) in Azure. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor de verkeers controle voor de Vm's voor ongebruikelijke activiteiten.

* [NSG-stroom logboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: als u uw virtuele machine (VM) gebruikt voor het hosten van webtoepassingen, gebruikt u een netwerk beveiligings groep (NSG) op het subnet van de virtuele machine om te beperken welk netwerk verkeer, poorten en protocollen mogen communiceren. Volg een minimale geprivilegieerde netwerk benadering bij het configureren van uw Nsg's zodat alleen het vereiste verkeer naar uw toepassing wordt toegestaan.

U kunt ook Azure Web Application firewall (WAF) voor essentiële webtoepassingen implementeren voor extra inspectie van inkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in.

* [Een toepassings gateway met een Web Application firewall maken met behulp van de Azure Portal](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

* [Virtuele netwerken en virtuele machines in azure](../network-overview.md)

* [Informatie over netwerk beveiligings groepen](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: DDoS-standaard beveiliging (Distributed Denial of service) op de virtuele netwerken inschakelen om tegen DDoS-aanvallen te beveiligen. Met Azure Security Center geïntegreerde bedreigings informatie kunt u communicatie met bekende schadelijke IP-adressen bewaken. Configureer Azure Firewall op elk van uw Virtual Network segmenten, met bedreigings informatie ingeschakeld en geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

U kunt de just-in-time-netwerk toegang van Azure Security Center gebruiken om de bloot stelling van Linux Virtual Machines aan de goedgekeurde IP-adressen te beperken gedurende een beperkte periode. U kunt ook Azure Security Center adaptieve netwerk beveiliging gebruiken om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

* [DDoS-beveiliging configureren](../../ddos-protection/manage-ddos-protection.md)

* [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

* [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../../security-center/azure-defender.md)

* [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](../../security-center/security-center-adaptive-network-hardening.md)

* [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](../../security-center/security-center-just-in-time.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: u kunt NSG-stroom logboeken vastleggen in een opslag account om stroom records te genereren voor uw Azure-virtual machines. Bij het onderzoeken van afwijkende activiteiten kunt u Network Watcher pakket vastleggen inschakelen, zodat het netwerk verkeer kan worden gecontroleerd op ongebruikelijke en onverwachte activiteiten.

* [NSG-stroom logboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher inschakelen](../../network-watcher/network-watcher-create.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: door pakket opnames te combi neren die worden verstrekt door Network Watcher en een open-source-id-hulp programma, kunt u de detectie van het netwerk binnendringen voor een breed scala aan bedreigingen. U kunt ook Azure Firewall op de Virtual Network-segmenten implementeren als dat nodig is, met bedreigings informatie die is ingeschakeld en die is geconfigureerd voor waarschuwing en weigeren voor schadelijk netwerk verkeer.

* [Detectie van binnendringing van het netwerk met Network Watcher en open source-hulpprogram ma's uitvoeren](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

* [Waarschuwingen configureren met Azure Firewall](../../firewall/threat-intel.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Hulp**: u kunt Azure-toepassing Gateway implementeren voor webtoepassingen waarvoor https/SSL is ingeschakeld voor vertrouwde certificaten. Met Azure-toepassing gateway stuurt u het webverkeer van uw toepassing naar specifieke bronnen door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-end-groep zoals virtuele Linux-machines.

* [Application Gateway implementeren](../../application-gateway/quick-create-portal.md)

* [Application Gateway configureren voor het gebruik van HTTPS](../../application-gateway/create-ssl-portal.md)

* [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](../../application-gateway/overview.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall die zijn geconfigureerd voor uw virtuele Azure-machines. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

* [Service Tags begrijpen en gebruiken](../../virtual-network/service-tags-overview.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor Azure virtual machines (VM) met behulp van Azure Policy. U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure VM-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, roltoewijzingen en Azure Policy toewijzingen, in één blauw druk te definiëren. U kunt de blauw druk Toep assen op abonnementen en resource beheer inschakelen met behulp van de blauw druk-versie.

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Voor beelden Azure Policy voor netwerken](../../governance/policy/samples/built-in-policies.md#network)

* [Een Azure Blueprint maken](../../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: u kunt tags gebruiken voor nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die is geconfigureerd voor uw virtuele Azure-machines. Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om de bedrijfs behoeften en/of de duur op te geven voor regels die verkeer naar/van een netwerk toestaan.

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

* [Een Virtual Network maken](../../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: gebruik het Azure-activiteiten logboek voor het bewaken van wijzigingen in de netwerk bron configuraties die zijn gerelateerd aan uw virtuele machines. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer wijzigingen in essentiële netwerk instellingen of bronnen plaatsvinden.

Gebruik Azure Policy om configuraties te valideren (en/of te herstellen) voor netwerk bronnen die betrekking hebben op Linux Virtual Machines.

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Waarschuwingen maken in Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Voor beelden Azure Policy voor netwerken](../../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt tijd bronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijd synchronisatie-instellingen voor uw Linux virtual machines te beheren.

* [Tijd synchronisatie configureren voor Azure Compute-resources](./time-sync.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: de Azure Security Center biedt bewaking van beveiligings logboeken voor Linux virtual machines.

* [Gegevensverzameling in Azure Security Center](../../security-center/security-center-enable-data-collection.md)

* [Als u de syslog-gegevens voor bewaking wilt vastleggen, moet u de uitbrei ding Log Analytics inschakelen](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken kunnen worden gebruikt voor het controleren van bewerkingen en acties die worden uitgevoerd op virtuele-machine bronnen. Het activiteiten logboek bevat alle schrijf bewerkingen (PUT, POST, DELETE) voor uw resources, behalve Lees bewerkingen (GET). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel de verzameling diagnostische gegevens van het gast besturingssysteem in door de diagnostische uitbrei ding op uw Virtual Machines (VM) te implementeren. U kunt de diagnostische extensie gebruiken om diagnostische gegevens te verzamelen, zoals toepassings Logboeken of prestatie meter items van een virtuele machine van Azure.

Voor een geavanceerde zicht baarheid van de toepassingen en services die worden ondersteund door uw virtuele machines, kunt u zowel Azure Monitor voor VM's als Application Insights inschakelen. Met Application Insights kunt u uw toepassing bewaken en telemetrie vastleggen, zoals HTTP-aanvragen, uitzonde ringen en anderen, zodat u problemen tussen de Vm's en uw toepassing kunt correleren.

Schakel Azure Monitor ook in voor toegang tot uw audit-en activiteiten logboeken, waaronder gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

* [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

* [Overzicht van de log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md)

* [Extensie van de virtuele machine voor logboek analyse voor Linux](../extensions/oms-linux.md)

* [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Overzicht van Application Insights](../../azure-monitor/app/app-insights-overview.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Hulp**: gebruik Azure Security Center om bewaking van beveiligings logboeken voor Azure virtual machines te bieden. Gezien het volume van de gegevens dat het beveiligings logboek genereert, wordt het niet standaard opgeslagen.

Als uw organisatie de gegevens van het beveiligings logboek van de virtuele machine wil behouden, kan deze worden opgeslagen in een Log Analytics-werk ruimte op de gewenste gegevensverzamelings tier die in Azure Security Center is geconfigureerd.

* [Gegevensverzameling in Azure Security Center](../../security-center/security-center-enable-data-collection.md)

* [Als u de syslog-gegevens voor bewaking wilt vastleggen, moet u de uitbrei ding Log Analytics inschakelen](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: Zorg ervoor dat voor opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van de logboeken van de virtuele machine, de Bewaar periode voor logboek registratie is ingesteld volgens de nalevings voorschriften van uw organisatie.

* [Virtuele machines in azure controleren](../../azure-monitor/insights/monitor-vm-azure.md)

* [De Bewaar periode van Log Analytics Workspace configureren](../../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: schakel de log Analytics-agent in, ook wel micro soft Monitoring Agent (MMA) of OMS Linux agent genoemd, en configureer deze voor het verzenden van logboeken naar een log Analytics-werk ruimte. De Linux-agent verzendt verzamelde gegevens uit verschillende bronnen naar uw Log Analytics-werk ruimte in Azure Monitor, evenals alle unieke Logboeken of metrische gegevens zoals gedefinieerd in een bewakings oplossing.

Analyseer en bewaak logboeken voor afwijkend gedrag en controleer regel matig de resultaten. Gebruik Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden om uw logboeken te controleren en te controleren.

* [Overzicht van de log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md)

* [Extensie van de virtuele machine voor logboek analyse voor Linux](../extensions/oms-linux.md)

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

* [Log Analytics-werk ruimte begrijpen](../../azure-monitor/log-query/log-analytics-tutorial.md)

* [Aangepaste query's uitvoeren in Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center geconfigureerd met een log Analytics-werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen voor uw Azure-virtual machines zijn gevonden.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden om waarschuwingen in te stellen voor afwijkende activiteiten.

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

* [Waarschuwingen beheren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

* [Een waarschuwing over logboek gegevens van log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: u hebt een hulp programma van derden nodig voor de detectie van zwakke plekken in het Linux-besturings systeem.

* [Instructies voor het voorbereiden van Linux-servers naar Azure Security Center](../../security-center/quickstart-onboard-machines.md)

* [Volgende koppeling bevat de aanbevolen beveiligings richtlijnen van micro soft, die kunnen dienen als een lijst met criteria voor de geselecteerde beveiligings software](../security-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie oplossing conform de behoeften van uw organisatie.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: u kunt console logboek registratie hand matig configureren per knoop punt en syslogs gebruiken om de gegevens op te slaan. U kunt ook de werk ruimte Log Analytics van Azure Monitor gebruiken om logboeken te controleren en query's uit te voeren op syslog-gegevens van virtuele Azure-machines.

* [Aangepaste query's uitvoeren in Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

* [Syslog-gegevensbronnen in Azure Monitor](../../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: Hoewel Azure Active Directory de aanbevolen methode is om gebruikers toegang te beheren, kunnen Azure virtual machines lokale accounts hebben. Zowel lokale als domein accounts moeten worden gecontroleerd en beheerd, normaal gesp roken met een minimale footprint. Gebruik Azure Privileged Identity Management bovendien voor beheerders accounts die worden gebruikt voor toegang tot de resources van de virtuele machine.

* [Informatie voor lokale accounts is beschikbaar op](../../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Informatie over privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Linux Virtual Machines en Azure Active Directory hebben geen standaard wachtwoorden. Klant die verantwoordelijk is voor toepassingen van derden en Marketplace-services die standaard wachtwoorden gebruiken.

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts die toegang hebben tot uw virtuele machines. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken. Beheerders accounts die worden gebruikt voor toegang tot resources van Azure virtual machine kunnen ook worden beheerd door Azure Privileged Identity Management (PIM). Azure Privileged Identity Management biedt verschillende opties, zoals just-in-time-uitbrei ding, waarbij Multi-Factor Authentication vereist voordat een rol en overdrachts opties worden aangenomen, zodat de machtigingen alleen beschikbaar zijn voor specifieke tijds frames en een goed keurder vereist is.

* [Inzicht in Azure Security Center identiteit en toegang](../../security-center/security-center-identity-access.md)

* [Informatie over privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: waar mogelijk, klant om SSO te gebruiken met Azure Active Directory in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [Informatie over eenmalige aanmelding met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

* [Identiteit en toegang bewaken in Azure Security Center](../../security-center/security-center-identity-access.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Identiteit en toegang bewaken in Azure Security Center](../../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

* [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure AD PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. De klant kan eventueel Azure Security Center waarschuwingen over risico detectie in Azure Monitor opnemen en aangepaste waarschuwingen/meldingen configureren met actie groepen.

* [Privileged Identity Management implementeren (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Meer informatie over Azure Security Center risico detecties (verdachte activiteiten)](../../active-directory/identity-protection/overview-identity-protection.md)

* [Azure-activiteitenlogboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](../../azure-monitor/platform/action-groups.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik Azure Active Directory beleid voor voorwaardelijke toegang en benoemde locaties om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties. U kunt beheerde identiteiten gebruiken om te verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code. Uw code die op een virtuele machine wordt uitgevoerd, kan de beheerde identiteit gebruiken om toegangs tokens aan te vragen voor services die ondersteuning bieden voor Azure AD-verificatie.

* [Een Azure AD-instantie maken en configureren](../../active-directory-domain-services/tutorial-create-instance.md)

* [Overzicht van beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u met behulp van Azure Active Directory-Beoordelingen voor identiteits toegang de groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. Wanneer u virtuele machines van Azure gebruikt, moet u de lokale beveiligings groepen en gebruikers controleren om ervoor te zorgen dat er geen onverwachte accounts zijn die inbreuk kunnen maken op het systeem.

* [Beoordelingen over Azure Identity Access gebruiken](../../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: Diagnostische instellingen configureren voor Azure Active Directory om de audit logboeken en aanmeldings logboeken te verzenden naar een log Analytics-werk ruimte. Gebruik Azure Monitor ook om logboeken te controleren en query's uit te voeren op auth syslog-gegevens van virtuele Azure-machines.

* [Log Analytics-werk ruimte begrijpen](../../azure-monitor/log-query/log-analytics-tutorial.md)

* [Azure-activiteitenlogboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Aangepaste query's uitvoeren in Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

* [Syslog-gegevensbronnen in Azure Monitor](../../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik de functies voor risico-en identiteits beveiliging van Azure Active Directory om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op de resources van uw opslag account. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren.

* [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in gevallen waarin een derde partij toegang moet hebben tot klant gegevens (zoals tijdens een ondersteunings aanvraag), gebruikt u klanten-lockbox voor virtuele machines van Azure om aanvragen voor toegang tot klant gegevens te controleren en goed te keuren of af te wijzen.

* [Klanten-lockbox voor Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om virtuele Azure-machines te volgen die gevoelige informatie opslaan of verwerken.

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of door een Azure Firewall. Voor Virtual Machines het opslaan of verwerken van gevoelige gegevens, implementeert u beleid en procedure (s) om ze uit te scha kelen wanneer ze niet worden gebruikt.

* [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

* [Een Virtual Network maken](../../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall implementeren](../../firewall/tutorial-firewall-deploy-portal.md)

* [Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall](../../firewall/threat-intel.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp** bij het implementeren van een oplossing van derden op netwerk verbindingen die controle bieden op niet-geautoriseerde overdracht van gevoelige informatie en die overdrachten blokkeert tijdens het melden van informatie over de beveiliging van uw mede werkers.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig voor de bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

* [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: gegevens in transit naar, van en tussen virtual machines (VM) waarop Linux wordt uitgevoerd, worden op een aantal manieren versleuteld, afhankelijk van de aard van de verbinding, bijvoorbeeld wanneer verbinding wordt gemaakt met een virtuele machine in een RDP-of SSH-sessie.

Micro soft maakt gebruik van het Transport Layer Security (TLS)-protocol voor het beveiligen van gegevens wanneer het onderweg is tussen de Cloud Services en klanten.

* [In-transit versleuteling in Vm's](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: gebruik een actief detectie hulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, met inbegrip van degenen die zich op locatie of op een externe service provider bevinden en werk de gevoelige informatie-inventaris van de organisatie bij.

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: met behulp van op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u taken in uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers op uw virtuele machine die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen voor de virtuele machine te geven, kunt u alleen bepaalde acties toestaan. U kunt toegangs beheer voor de virtuele machine configureren in het Azure Portal, met behulp van de Azure CLI of Azure PowerShell.

* [Azure RBAC](../../role-based-access-control/overview.md)

* [Ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: Implementeer een hulp programma van derden, zoals een geautomatiseerde oplossing voor het voor komen van gegevens verlies op basis van een host, om toegangs controles af te dwingen om het risico van gegevens schendingen te beperken.

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: virtuele schijven op Linux virtual machines (VM) worden versleuteld met behulp van versleuteling aan server zijde of Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van de DM-Crypt-functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels in de gast-VM. Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

* [Versleuteling aan server zijde van Azure Managed disks](../disk-encryption.md)

* [Azure Disk Encryption voor Linux-VM's](./disk-encryption-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden doorgevoerd in virtuele machines en gerelateerde resources.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../../azure-monitor/platform/alerts-activity-log.md)

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../../azure-monitor/platform/alerts-activity-log.md)

* [Azure Storage-analyselogboeken](../../storage/common/storage-analytics-logging.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: u hebt een hulp programma van derden nodig voor de detectie van zwakke plekken in het Linux-besturings systeem.

* [Instructies voor het voorbereiden van Linux-servers naar Azure Security Center](../../security-center/quickstart-onboard-machines.md)

* [Aanbevolen beveiligings richtlijnen voor micro soft](../security-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: gebruik de Azure updatebeheer-oplossing om updates en patches voor uw virtuele machines te beheren. Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor door patch ondersteunde systemen.

* [Updatebeheer oplossing in azure](../../automation/update-management/overview.md)

* [Updates en patches voor uw virtuele machines beheren](../../automation/update-management/manage-updates-for-vm.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: u kunt een oplossing voor patch beheer van derden gebruiken. U kunt de Azure Updatebeheer-oplossing gebruiken om updates en patches voor uw virtuele machines te beheren. Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor door patch ondersteunde systemen.

* [Updatebeheer oplossing in azure](../../automation/update-management/overview.md)

* [Updates en patches voor uw virtuele machines beheren](../../automation/update-management/manage-updates-for-vm.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: scan resultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligings problemen zijn opgelost. Bij het gebruik van aanbevelingen voor beveiligings beheer die worden voorgesteld door Azure Security Center, kan de klant in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

* [Azure Security Center beveiligde Score begrijpen](../../security-center/secure-score-security-controls.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief virtuele machines) in uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

* [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

* [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](../../role-based-access-control/overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch te organiseren op basis van een taxonomie.

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om virtuele machines en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

* [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor uw reken resources maken. U kunt ook adaptieve toepassings besturings elementen gebruiken, een functie van Azure Security Center om u te helpen bij het definiëren van een set toepassingen die op geconfigureerde machine groepen mogen worden uitgevoerd. Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

         

* [Adaptief toepassings beheer inschakelen](../../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: Azure** Policy gebruiken om beperkingen te geven aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen. Dit kan helpen bij omgevingen met hoge beveiliging, zoals die met opslag accounts.

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. Maak gebruik van Azure virtual machine Inventory om het verzamelen van informatie over alle software op Virtual Machines te automatiseren. Opmerking: de software naam, versie, uitgever en vernieuwings tijd zijn beschikbaar via de Azure Portal. Om toegang te krijgen tot metrische gegevens en andere informatie, is de klant verplicht om diagnose op gast niveau in te scha kelen en kan syslog-informatie naar een aangewezen opslag account worden verzonden.

Naast het gebruik van Wijzigingen bijhouden voor het bewaken van software toepassingen, kunnen besturings elementen voor adaptieve toepassingen in Azure Security Center machine learning gebruiken om de toepassingen te analyseren die worden uitgevoerd op uw computers en een acceptatie lijst te maken van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat ongewenste software in uw omgeving kan worden gebruikt. U kunt de controle modus of de afdwingings modus configureren. In de controle modus wordt alleen de activiteit op de beveiligde Vm's gecontroleerd. Met de afdwingings modus worden de regels afgedwongen en wordt ervoor gezorgd dat toepassingen die niet mogen worden uitgevoerd, worden geblokkeerd.

* [Diagnostische extensie voor virtuele Linux-machines](../extensions/diagnostics-linux.md?toc=/azure/azure-monitor/toc.json)

* [Een inleiding tot Azure Automation](../../automation/automation-intro.md)

* [Azure VM-inventaris inschakelen](../../automation/automation-tutorial-installed-software.md)

* [Meer informatie over adaptieve toepassings besturings elementen](../../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. U kunt Wijzigingen bijhouden gebruiken om alle software te identificeren die op Virtual Machines is geïnstalleerd. U kunt uw eigen proces implementeren of de configuratie van Azure Automation status gebruiken voor het verwijderen van onbevoegde software.

* [Een inleiding tot Azure Automation](../../automation/automation-intro.md)

* [Wat is Wijzigingen bijhouden?](../../automation/change-tracking/overview.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Hulp**: gebruik Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure virtual machines.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: Azure** Policy gebruiken om beperkingen te geven aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Een specifiek resource type weigeren met Azure Policy](../../governance/policy/samples/index.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: adaptief toepassings beheer is een intelligente, geautomatiseerde en end-to-end oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines (Windows en Linux). Implementeer een oplossing van derden als deze niet voldoet aan de vereisten van uw organisatie.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken. U kunt ook gebruikmaken van Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: toepassingen met een hoog risico die zijn geïmplementeerd in uw Azure-omgeving, kunnen worden geïsoleerd met virtuele netwerken, subnetten, abonnementen, beheer groepen en voldoende beveiligd met een Azure firewall, Web Application firewall (WAF) of netwerk beveiligings groep (NSG).

* [Virtuele netwerken en virtuele machines in azure](../network-overview.md)

* [Overzicht van Azure Firewall](../../firewall/overview.md)

* [Overzicht van Web Application firewall](../../web-application-firewall/overview.md)

* [Overzicht van netwerkbeveiliging](../../virtual-network/network-security-groups-overview.md)

* [Overzicht van Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)

* [Uw resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md)

* [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy of Azure Security Center om beveiligings configuraties voor alle Azure-resources te onderhouden. Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw bedrijf of deze overschrijden.

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Informatie over het downloaden van de VM-sjabloon](../windows/download-template.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: gebruik Azure Security Center aanbeveling [beveiligings configuraties op uw virtual machines herstellen] voor het onderhouden van beveiligings configuraties op alle reken resources.

* [Azure Security Center aanbevelingen bewaken](../../security-center/security-center-recommendations.md)

* [Azure Security Center aanbevelingen herstellen](../../security-center/security-center-remediate-recommendations.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Resource Manager sjablonen en Azure-beleid om Azure-resources die zijn gekoppeld aan de virtuele machines, veilig te configureren. Azure Resource Manager sjablonen zijn JSON-bestanden die worden gebruikt voor het implementeren van de virtuele machine, samen met Azure-resources en aangepaste sjablonen moeten worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Informatie over het maken van Azure Resource Manager sjablonen](../windows/ps-template.md)

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Wat zijn Azure Policy effecten?](../../governance/policy/concepts/effects.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: er zijn verschillende opties voor het onderhouden van een veilige configuratie voor Azure virtual machines (VM) voor implementatie:

1-Azure Resource Manager sjablonen: Dit zijn JSON-bestanden die worden gebruikt voor het implementeren van een virtuele machine vanuit de Azure Portal, en aangepaste sjabloon moet worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen.

2-aangepaste virtuele harde schijf (VHD): in sommige gevallen kan het vereist zijn dat er aangepaste VHD-bestanden worden gebruikt, zoals bij het verwerken van complexe omgevingen die niet op een andere manier kunnen worden beheerd.

3-Azure Automation status configuratie: zodra het basis besturingssysteem is geïmplementeerd, kan dit worden gebruikt voor een gedetailleerdere controle van de instellingen en afgedwongen via het Automation-Framework.

In de meeste gevallen kunnen de micro soft-sjablonen voor virtuele machines in combi natie met de Azure Automation desired state Configuration worden geholpen bij het verg Roten en onderhouden van de beveiligings vereisten.

* [Informatie over het downloaden van de VM-sjabloon](../windows/download-template.md)

* [Informatie over het maken van ARM-sjablonen](../windows/ps-template.md)

* [Een aangepaste VM-VHD uploaden naar Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910&preserve-view=true)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps/opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities, Azure Resource Manager sjablonen, gewenste status configuratie scripts en andere code. Voor toegang tot de resources die u beheert in azure DevOps, zoals uw code, builds en het bijhouden van wijzigingen, moet u machtigingen hebben voor deze specifieke resources. De meeste machtigingen worden verleend via ingebouwde beveiligings groepen, zoals beschreven in machtigingen en toegang. U kunt machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als dit is geïntegreerd met TFS.

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt (bijvoorbeeld virtuele harde schijf), gebruikt u Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

* [Meer informatie over Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Azure RBAC configureren](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: maakt gebruik van Azure Policy om systeem configuraties voor uw virtuele machines te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

* [Azure-beleid configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Hulp**: de configuratie van de Azure Automation-status is een configuratie beheer service voor de desired state Configuration-knoop punten in elke Cloud of een on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](../../automation/automation-dsc-onboarding.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: Maak gebruik van Azure Security Center voor het uitvoeren van basislijn scans voor uw virtuele machines in Azure. Aanvullende methoden voor automatische configuratie zijn onder andere het gebruik van Azure Automation status configuratie.

* [Aanbevelingen herstellen in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

* [Aan de slag met de configuratie van de Azure Automation-status](../../automation/automation-dsc-getting-started.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: de configuratie van de Azure Automation-status is een configuratie beheer service voor de desired state Configuration-knoop punten in elke Cloud of een on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](../../automation/automation-dsc-onboarding.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

* [Integratie met door Azure beheerde identiteiten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Een Key Vault maken](../../key-vault/general/quick-create-portal.md)

* [Verifiëren bij Key Vault](../../key-vault/general/authentication.md)

* [Toegangs beleid voor Key Vault toewijzen](../../key-vault/general/assign-access-policy-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten voor het leveren van Azure-Services met een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

* [Beheerde identiteiten configureren](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: u hebt een hulp programma van derden nodig voor beveiliging tegen schadelijke software in azure Linux virtual machine.

* [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](../security-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing op virtuele machines van Azure omdat het een reken resource is.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: u hebt een hulp programma van derden nodig voor beveiliging tegen schadelijke software in azure Linux virtual machine.

* [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](../security-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp** bij het inschakelen van Azure backup en het configureren van de virtuele machines van Azure (VM), evenals de gewenste frequentie en bewaar periode voor automatische back-ups.

* [Een overzicht van Azure VM backup](../../backup/backup-azure-vms-introduction.md)

* [Een back-up van een Azure VM maken op basis van de VM-instellingen](../../backup/backup-azure-vms-first-look-arm.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: Maak moment opnamen van uw virtuele Azure-machines of de beheerde schijven die zijn gekoppeld aan deze instanties met behulp van Power shell of rest-api's. Maak een back-up van door de klant beheerde sleutels binnen Azure Key Vault.

Schakel Azure Backup en doel-Azure-Virtual Machines (VM) in, evenals de gewenste frequentie en retentie perioden. Dit omvat de volledige back-up van de systeem status. Als u gebruikmaakt van Azure Disk Encryption, wordt de back-up van door de klant beheerde sleutels automatisch door Azure VM backup verwerkt.

* [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](../../backup/backup-azure-vms-encryption.md)

* [Overzicht van Azure VM backup](../../backup/backup-azure-vms-introduction.md)

* [Begrijpen hoe automatische back-ups werken](../../backup/backup-azure-vms-encryption.md)

* [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat het gegevens herstel van inhoud regel matig wordt uitgevoerd in azure backup. Test zo nodig inhoud terug naar een geïsoleerd virtueel netwerk of abonnement. Klant om het herstel van back-ups van door de klant beheerde sleutels te testen.

Als u gebruikmaakt van Azure Disk Encryption, kunt u de Azure VM herstellen met de versleutelings sleutels voor de schijf. Wanneer u schijf versleuteling gebruikt, kunt u de Azure VM herstellen met de versleutelings sleutels voor de schijf.

* [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](../../backup/backup-azure-vms-encryption.md)

* [Bestanden herstellen vanuit back-up van Azure virtual machine](../../backup/backup-azure-restore-files-from-vm.md)

* [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Een back-up maken van een versleutelde VM en deze herstellen](../../backup/backup-azure-vms-encryption.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: wanneer u back-ups maakt van virtuele Azure-machines met Azure backup, worden virtuele machines op rest versleuteld met Storage service Encryption (SSE). Azure Backup kunt ook een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption. Azure Disk Encryption is ook geïntegreerd met Azure Key Vault Key Encryption Keys (KEKs). Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. 

* [Voorlopig verwijderen voor Vm's](../../backup/soft-delete-virtual-machines.md)

* [Azure Key Vault: overzicht van voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

* [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

* [Tags gebruiken om Azure-resources te organiseren](../../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen.

* [Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema. Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

* [Continue export configureren](../../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

* [Werk stroom automatisering en Logic Apps configureren](../../security-center/workflow-automation.md)

**Azure Security Center bewaking**: niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

* [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../../security/benchmarks/security-baselines-overview.md)
