---
title: Azure-beveiligings basislijn voor Azure Automation
description: Azure-beveiligings basislijn voor Automation
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea984e527eb526e49c87ce5385ba5715803cc72d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737249"
---
# <a name="azure-security-baseline-for-automation"></a>Azure-beveiligings basislijn voor Automation

De Azure-beveiligings basislijn voor Automation bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Azure Automation-account biedt nog geen ondersteuning voor persoonlijke Azure-koppelingen voor het beperken van de toegang tot de service via persoonlijke eind punten. Runbooks die worden geverifieerd en uitgevoerd op resources in azure die worden uitgevoerd op een Azure-sandbox en gebruikmaken van gedeelde back-end-bronnen, die micro soft verantwoordelijk is voor het isoleren van elkaar; hun netwerken zijn onbeperkt en hebben toegang tot open bare bronnen. Azure Automation heeft momenteel geen virtuele netwerk integratie voor particuliere netwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Voor meer isolatie van uw runbooks kunt u Hybrid Runbook Workers gebruiken die worden uitgevoerd op virtuele machines van Azure. Wanneer u een virtuele machine van Azure maakt, moet u een virtueel netwerk (VNet) maken of een bestaand VNet gebruiken en uw Vm's configureren met een subnet. Zorg ervoor dat voor alle geïmplementeerde subnetten een netwerk beveiligings groep is toegepast met netwerk toegangs beheer die specifiek is voor uw toepassingen vertrouwde poorten en bronnen. Raadpleeg voor specifieke service vereisten de beveiligings aanbeveling voor die specifieke service.

Als u een specifieke vereiste hebt, kan Azure Firewall ook worden gebruikt om hieraan te voldoen.

* [Virtuele netwerken en virtuele machines in azure](../virtual-machines/network-overview.md)

* [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook Execution Environment](./automation-runbook-execution.md#runbook-execution-environment)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: Azure Automation momenteel geen virtuele netwerk integratie voor particuliere netwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door Azure virtual machines, moet u ervoor zorgen dat het subnet dat deze werk rollen bevat, zijn ingeschakeld met een netwerk beveiligings groep (NSG) en dat de stroom logboeken worden geconfigureerd om logboeken door te sturen naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken door sturen naar een Log Analytics-werk ruimte en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Hoewel NSG-regels en door de gebruiker gedefinieerde routes niet van toepassing zijn op een privé-eind punt, worden NSG-stroom logboeken en controle gegevens voor uitgaande verbindingen nog steeds ondersteund en kunnen ze worden gebruikt.

* [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: Azure Automation heeft momenteel geen virtuele netwerk integratie voor particuliere netwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door virtuele machines van Azure, moet u DDoS-standaard beveiliging (Distributed Denial of service) inschakelen voor uw virtuele netwerken die als host fungeren voor uw Hybrid Runbook Workers om DDoS-aanvallen te weren. Door Azure Security Center geïntegreerde bedreigings informatie te gebruiken, kunt u communicatie met bekende schadelijke IP-adressen weigeren. Configureer Azure Firewall op elk van de Virtual Network segmenten, met bedreigings informatie ingeschakeld, en configureer deze voor **waarschuwing en weigeren** voor schadelijk netwerk verkeer.

U kunt de just-in-time-netwerk toegang van Azure Security Center gebruiken om de bloot stelling van virtuele Windows-machines gedurende een beperkte periode te beperken tot de goedgekeurde IP-adressen. U kunt ook de aanbevelingen van Azure Security Center Adaptive netwerk beveiliging voor NSG-configuraties gebruiken om poorten en bron-Ip's te beperken op basis van daad werkelijk verkeer en bedreigings informatie.

* [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

* [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

* [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

* [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](../security-center/security-center-adaptive-network-hardening.md)

* [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](../security-center/security-center-just-in-time.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: Azure Automation momenteel geen virtuele netwerk integratie heeft voor particulier netwerk buiten de ondersteuning voor Hybrid Runbook Workers, is dit besturings element niet van toepassing als u gebruikmaakt van de out-of-the-Box Service zonder Hybrid Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door virtuele machines van Azure, kunt u NSG-stroom logboeken vastleggen in een opslag account om stroom records te genereren voor uw Azure-Virtual Machines die fungeren als Runbook-werk nemers. Bij het onderzoeken van afwijkende activiteiten kunt u Network Watcher pakket vastleggen inschakelen, zodat het netwerk verkeer kan worden gecontroleerd op ongebruikelijke en onverwachte activiteiten.

* [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Azure Automation heeft momenteel geen virtuele netwerk integratie voor particuliere netwerken, naast de ondersteuning voor Hybrid Runbook Workers. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden gehost op virtuele machines van Azure, kunt u pakket opnames combi neren die worden verstrekt door Network Watcher-en open source-id-hulpprogram ma's om netwerk inbreuken te detecteren voor een breed scala aan bedreigingen voor deze werk computers. U kunt ook Azure Firewall op de Virtual Network-segmenten implementeren waar nodig, met bedreigings informatie die is ingeschakeld en die is geconfigureerd voor waarschuwing en weigeren voor schadelijk netwerk verkeer.

* [Detectie van binnendringing van het netwerk met Network Watcher en open source-hulpprogram ma's uitvoeren](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

* [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags voor het definiëren van netwerk toegangs beheer voor netwerk beveiligings groepen of Azure firewall die zijn geconfigureerd in Azure en die toegang nodig hebben tot uw Automation-resources. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld GuestAndHybridManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

* [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk bronnen die worden gebruikt door Azure Automation met Azure Policy.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturings elementen en beleids regels, in één blauw definitie te voorzien. U kunt de blauw druk Toep assen op nieuwe abonnementen en het beheer en beheer verfijnen met behulp van versies.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Voor beelden Azure Policy voor netwerken](../governance/policy/samples/built-in-policies.md#network)

* [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen in uw netwerk bronnen te detecteren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Waarschuwingen maken in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt tijd bronnen voor Azure-resources. U hebt echter de mogelijkheid om de tijd synchronisatie-instellingen te beheren voor Hybrid Runbook workers die worden uitgevoerd op virtuele Windows-machines.

* [Tijd synchronisatie configureren voor Azure Compute-resources](../virtual-machines/windows/time-sync.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp** bij het door sturen van logboek gegevens naar Azure monitor logboeken voor het verzamelen van beveiligings gegevens die door Azure Automation resources worden gegenereerd. Gebruik in Azure Monitor logboek query's om analyses te zoeken en uit te voeren, en gebruik Azure Storage accounts voor lange termijn/archiverings opslag. Azure Automation kunt de status van de runbook-taak, taak stromen, configuratie gegevens van de automatiserings status, update beheer en het bijhouden van wijzigingen of inventaris logboeken verzenden naar uw werk ruimte Log Analytics. Deze informatie is zichtbaar via de API Azure Portal, Azure PowerShell en Azure Monitor logs, waarmee u eenvoudig onderzoek kunt uitvoeren.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

* [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken](./automation-manage-send-joblogs-log-analytics.md)

* [DSC integreren met Azure Monitor-logboeken](./automation-dsc-diagnostics.md)

* [Ondersteunde regio's voor gekoppelde Log Analytics-werkruimte](./how-to/region-mappings.md)

* [Updatebeheer logboeken opvragen](./update-management/query-logs.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Azure monitor in voor toegang tot uw audit-en activiteiten logboeken, waaronder gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

* [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: wanneer u Azure Automation gebruikt met de werk nemers met meerdere tenants, is dit besturings element niet van toepassing en worden de onderliggende virtuele machines door het platform verwerkt.

Wanneer u de functie Hybrid Runbook Worker gebruikt, bevat Azure Security Center beveiligings logboek bewaking voor virtuele Windows-machines. Als uw organisatie de gegevens van het beveiligings gebeurtenis logboek wil behouden, kan deze worden opgeslagen in een gegevensverzamelings tier, op welk moment een query kan worden uitgevoerd in Log Analytics. Er zijn verschillende lagen: Mini maal, algemeen en alle, die worden beschreven in de volgende koppeling.

* [De gegevensverzamelings laag configureren in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

* [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Details van gegevens retentie voor Automation-accounts](./automation-managing-data.md#data-retention)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig resultaten bekijken. Gebruik Azure Monitor-logboek query's om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

* [Logboek query's in Azure Monitor begrijpen](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center met Azure monitor voor bewaking en waarschuwingen voor afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden.

U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

* [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Een waarschuwing over Azure Monitor logboek gegevens](../azure-monitor/learn/tutorial-response.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: wanneer u Azure Automation met meerdere Tenant werk rollen gebruikt, is dit besturings element niet van toepassing en worden de onderliggende virtuele machines door het platform verwerkt.

Wanneer u de functie Hybrid Runbook Worker gebruikt, kunt u echter gebruikmaken van micro soft anti-malware voor Azure Cloud Services en virtuele machines. Configureer uw virtuele machines om gebeurtenissen te registreren in een Azure Storage-account. Configureer een Log Analytics-werk ruimte om de gebeurtenissen van de opslag accounts op te nemen en maak waar nodig waarschuwingen. Volg de aanbevelingen in Azure Security Center: ' COMPUTE &amp; apps '.

* [Micro soft anti-malware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

* [Bewaking op gast niveau inschakelen voor virtuele machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie oplossing conform uw organisatie.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: wanneer u Azure Automation gebruikt met de werk nemers met meerdere tenants, is dit besturings element niet van toepassing en worden de onderliggende virtuele machines door het platform verwerkt.

Wanneer u echter de functie Hybrid Runbook Worker gebruikt, biedt Azure Security Center beveiligings logboek bewaking voor virtuele machines van Azure. Security Center voorziet de Log Analytics agent op alle ondersteunde virtuele machines van Azure en nieuwe die worden gemaakt als automatisch inrichten is ingeschakeld. U kunt de agent ook hand matig installeren. De agent maakt de gebeurtenis 4688 voor het maken van processen en het veld commandline in gebeurtenis 4688 mogelijk. Nieuwe processen die op de virtuele machine worden gemaakt, worden vastgelegd door het gebeurtenis logboek en worden bewaakt door de detectie services van Security Center.

* [Gegevensverzameling in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: gebruik Azure Active Directory ingebouwde beheerders rollen die expliciet kunnen worden toegewezen en waarop query's kunnen worden uitgevoerd. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen. Wanneer u een Automation-account uitvoert als accounts voor uw runbooks, moet u ervoor zorgen dat deze service-principals ook worden bijgehouden in uw inventaris, omdat deze vaak verhoogde machtigingen hebben. Verwijder ongebruikte run as-accounts om uw blootgestelde aanvals oppervlak te minimaliseren.

* [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Een Azure Automation uitvoeren als-account beheren](./manage-runas-account.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Automation-account heeft niet het concept standaard wachtwoord. Klanten zijn verantwoordelijk voor toepassingen van derden en Marketplace-services die gebruik kunnen maken van de standaard wachtwoorden die boven op de service of de Hybrid Runbook Workers worden uitgevoerd.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken. Wanneer u een Automation-account uitvoert als accounts voor uw runbooks, moet u ervoor zorgen dat deze service-principals ook worden bijgehouden in uw inventaris, omdat deze vaak verhoogde machtigingen hebben. Bereik deze identiteiten met de mini maal privileged permissions die ze nodig hebben om ervoor te zorgen dat uw runbooks hun geautomatiseerde proces kunnen uitvoeren. Verwijder ongebruikte run as-accounts om uw blootgestelde aanvals oppervlak te minimaliseren.

U kunt ook Just-in-time/alleen-voldoende toegang inschakelen met behulp van Azure AD Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure Resource Manager.

* [Meer informatie over Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Een Azure Automation uitvoeren als-account beheren](./manage-runas-account.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik waar mogelijk SSO met Azure Active Directory in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [Eenmalige aanmelding bij toepassingen in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

* [Azure AD gebruiken voor verificatie bij Azure](./automation-use-azure-ad.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure AD multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: gereserveerde computers voor alle beheer taken gebruiken

**Richt lijnen**: gebruik paw's met multi-factor Authentication om u aan te melden bij en te configureren Azure Automation account bronnen in productie omgevingen.

* [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Richt lijnen**: gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. De klant kan eventueel waarschuwingen voor Azure Security Center risico detectie door sturen naar Azure Monitor en aangepaste waarschuwingen/meldingen configureren met actie groepen.

* [Meer informatie over Azure Security Center risico detecties (verdachte activiteiten)](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](../azure-monitor/platform/action-groups.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: het wordt aanbevolen om de benoemde locaties voor voorwaardelijke toegang te gebruiken om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Richt lijnen**: Azure AD gebruiken als het centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties. Als u Hybrid Runbook Workers gebruikt, kunt u beheerde identiteiten gebruiken in plaats van run as-accounts om naadloze beveiligde machtigingen in te scha kelen.

* [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

* [Runbook-verificatie gebruiken met beheerde identiteiten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. Wanneer u een run as-account voor Automation-accounts gebruikt voor uw runbooks, moet u ervoor zorgen dat deze service-principals ook worden bijgehouden in uw inventaris, omdat deze vaak verhoogde machtigingen hebben. Verwijder ongebruikte run as-accounts om uw blootgestelde aanvals oppervlak te minimaliseren.

* [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

* [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

* [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Een Azure Automation uitvoeren als-account beheren](./manage-runas-account.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot de Azure AD-aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

* [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: Azure AD-functies voor risico-en identiteits beveiliging gebruiken voor het configureren van automatische antwoorden op gedetecteerde verdachte acties met betrekking tot de gebruikers identiteiten voor uw netwerk bron. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

* [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: voor Azure Automation accounts kan micro soft-ondersteuning toegang krijgen tot de meta gegevens van platform bronnen tijdens een open ondersteunings aanvraag zonder gebruik van een ander hulp programma.

Als er echter Hybrid Runbook Workers worden gebruikt die worden ondersteund door Azure virtual machines en een derde partij toegang heeft tot klant gegevens (zoals tijdens een ondersteunings aanvraag), gebruikt u Klanten-lockbox (preview) voor virtuele machines van Azure om aanvragen voor toegang tot klant gegevens te controleren en goed te keuren of af te wijzen.

* [Wat is Klanten-lockbox?](../security/fundamentals/customer-lockbox-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure Automation resources die gevoelige informatie opslaan of verwerken.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Geïsoleerde omgevingen door gebruik te maken van afzonderlijke Automation-account resources. Resources zoals Hybrid Runbook Workers moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of Azure Firewall. Voor virtuele machines die gevoelige gegevens opslaan of verwerken, implementeert u beleid en procedure (s) om ze uit te scha kelen wanneer ze niet worden gebruikt.

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

* [Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: wanneer u de functie Hybrid Runbook worker gebruikt, maakt u gebruik van een oplossing van een derde partij op netwerk verbindingen die controleert op niet-geautoriseerde overdracht van gevoelige informatie en worden dergelijke overdrachten geblokkeerd tijdens het melden van informatie over de beveiliging van uw mede werkers.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en beschermd tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

* [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources in virtuele netwerken van Azure, TLS 1,2 of hoger kunnen onderhandelen. Azure Automation volledig ondersteunt en afdwingt trans port Layer (TLS) 1,2 en alle client aanroepen of latere versies voor alle externe HTPS-eind punten (via webhooks, DSC-knoop punten, hybride runbook worker).

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

* [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation TLS 1,2 afdwingen](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: gebruik een actief detectie hulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, met inbegrip van degenen die zich op locatie of op een externe service provider bevinden en werk de gevoelige informatie-inventaris van de organisatie bij.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure-functies voor op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van de toegang tot Azure Automation resources met behulp van de ingebouwde roldefinities, toegang toewijzen voor gebruikers die toegang hebben tot uw Automation-resources met een mini maal privileged of ' just-genoeg '-toegangs model. Wanneer u Hybrid Runbook Workers gebruikt, moet u beheerde identiteiten voor die virtuele machines gebruiken om service-principals te vermijden, wanneer u de werk nemers met meerdere tenants of Hybrid Runbook gebruikt om te zorgen dat de Azure RBAC-machtigingen op de juiste manier zijn afgestemd op de identiteit van de Runbook-werk nemers.

* [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

* [Runbook-machtigingen voor een Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Rolmachtigingen en beveiliging beheren](./automation-role-based-access-control.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: Azure Automation worden momenteel niet de virtuele machines van de onderliggende multi tenant-runbook worker beschikbaar en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door Azure virtual machines, moet u een oplossing voor preventie van gegevens verlies op basis van derden gebruiken om toegangs beheer voor uw gehoste Hybrid Runbook Worker virtuele machines af te dwingen.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: gebruik door de klant beheerde sleutels met Azure Automation. Azure Automation ondersteunt het gebruik van door de klant beheerde sleutels voor het versleutelen van alle ' veilige assets ', zoals: referenties, certificaten, verbindingen en versleutelde variabelen. Maak gebruik van versleutelde variabelen met uw runbooks voor al uw permanente variabele lookups om onbedoelde bloot stelling te voor komen.

Bij het gebruik van Hybrid Runbook Workers worden de virtuele schijven op de virtuele machines op rest versleuteld met behulp van server versleuteling of Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows voor het versleutelen van beheerde schijven met door de klant beheerde sleutels in de gast-VM. Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

* [Versleuteling aan server zijde van Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption voor Windows-Vm's](../virtual-machines/windows/disk-encryption-overview.md)

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Beheerde variabelen in Azure Automation](./shared-resources/variables.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in essentiële Azure-resources, zoals netwerk onderdelen, Azure Automation accounts en runbooks.

* [Diagnostische logboek registratie voor een netwerk beveiligings groep](../private-link/private-link-overview.md#logging-and-monitoring)

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg de aanbevelingen van Azure Security Center over het uitvoeren van beveiligings evaluaties voor uw Azure-resources

* [Aanbevelingen voor beveiliging in Azure Security Center](../security-center/security-center-recommendations.md)

* [Naslag informatie over Security Center aanbeveling](../security-center/recommendations-reference.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: Azure Automation worden momenteel niet de virtuele machines van de onderliggende multi tenant-runbook worker beschikbaar en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door virtuele machines van Azure, kunt u Azure Updatebeheer gebruiken om updates en patches voor uw virtuele machines te beheren. Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Met hulpprogram ma's als System Center Updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in Windows Server Update Services (WSUS). Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden.

* [Updatebeheer in azure](./update-management/overview.md)

* [Updates en patches voor uw virtuele machines beheren](./update-management/manage-updates-for-vm.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Hulp**: Azure Automation worden momenteel niet de virtuele machines van de onderliggende multi tenant-runbook worker beschikbaar en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Als u Hybrid Runbook Workers gebruikt die worden ondersteund door virtuele machines van Azure, kunt u Azure Updatebeheer gebruiken om updates en patches voor uw virtuele machines te beheren. Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Met hulpprogram ma's als System Center Updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in Windows Server Update Services (WSUS). Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden.

* [Updatebeheer oplossing in azure](./update-management/overview.md)

* [Updates en patches voor uw Azure-VM's beheren](./update-management/manage-updates-for-vm.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: scan resultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligings problemen zijn opgelost. Bij het gebruik van aanbevelingen voor beveiligings beheer die worden voorgesteld door Azure Security Center, kan de klant in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center om prioriteiten te stellen voor het herstel van ontdekte beveiligings problemen.

* [Azure Security Center beveiligde Score begrijpen](../security-center/secure-score-security-controls.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle Azure Automation-resources binnen uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

* [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

* [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure Automation-resources in te delen en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement. Verwijder ongebruikte run as-accounts om uw blootgestelde aanvals oppervlak te minimaliseren.

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Een Azure Automation uitvoeren als-account beheren](./manage-runas-account.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources maken conform uw organisatie behoeften.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Daarnaast kunt u met de Azure-resource grafiek bronnen in abonnementen opvragen/ontdekken. Dit kan helpen bij omgevingen met hoge beveiliging, zoals die met opslag accounts.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

* [Azure Policy voor beeld van ingebouwde invoeg toepassingen voor Azure Automation](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: de virtuele machines van de onderliggende multi tenant-runbook worker worden momenteel niet weer gegeven door de Azure Automation-aanbieding en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Workers. Het is echter mogelijk om de Power shell-of python-modules te installeren, te verwijderen en te beheren die door runbooks toegankelijk zijn via de portal of cmdlets. De module ungoedgekeurd of Old moet worden verwijderd of bijgewerkt voor de runbooks.

Als u Hybrid Runbook Workers gebruikt die door Azure worden ondersteund Virtual Machines is Azure Automation volledige controle over de implementatie, het uitvoeren van bewerkingen en het buiten gebruik stellen van werk belastingen en resources. Maak gebruik van Azure virtual machine Inventory om het verzamelen van informatie over alle software op Virtual Machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Om toegang te krijgen tot de installatie datum en andere informatie, is de klant verplicht om diagnostische gegevens op gast niveau in te scha kelen en de Windows-gebeurtenis logboeken naar een Log Analytics-werk ruimte te brengen.

* [Een inleiding tot Azure Automation](./automation-intro.md)

* [Azure VM-inventaris inschakelen](./automation-tutorial-installed-software.md)

* [Module in Azure Automation beheren](./shared-resources/modules.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: klanten kunnen voor komen dat resources worden gemaakt of gebruikt met Azure policy zoals vereist door de bedrijfs richtlijnen van de klant. U kunt uw eigen proces voor het verwijderen van niet-geautoriseerde resources implementeren. Binnen de Azure Automation kunt u de Power shell-of python-modules installeren, verwijderen en beheren die door runbooks toegankelijk zijn via de portal of cmdlets. De module ungoedgekeurd of Old moet worden verwijderd of bijgewerkt voor de runbooks.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Module in Azure Automation beheren](./shared-resources/modules.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: wanneer u de functie Hybrid Runbook worker gebruikt, kunt u Azure Security Center adaptieve toepassings besturings elementen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van Azure virtual machines.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: wanneer u de functie Hybrid Runbook worker gebruikt, kunt u de functie Azure Security Center adaptieve toepassings besturings elementen gebruiken met de virtuele machines van uw Hybrid Worker.

Adaptief toepassings beheer is een intelligente, geautomatiseerde en end-to-end oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines (Windows en Linux). Implementeer een oplossing van derden als deze niet voldoet aan de vereisten van uw organisatie.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik Azure-beleid voor voorwaardelijke toegang om de interactie van gebruikers met Azure Resource Manager te beperken door het configureren van ' toegang blok keren ' voor de app Microsoft Azure beheer van niet-beveiligde of niet-goedgekeurde locaties of apparaten.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: wanneer u de functie Hybrid Runbook worker gebruikt en afhankelijk van het type scripts, kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken. U kunt ook gebruikmaken van Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

* [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: toepassingen met een hoog risico die in uw Azure-omgeving zijn geïmplementeerd, kunnen worden geïsoleerd met behulp van afzonderlijke netwerk-en resource containers met behulp van constructs als virtuele netwerken, subnetten, abonnementen, beheer groepen, ze kunnen voldoende worden beveiligd met een Azure firewall, Web Application firewall (WAF) of netwerk beveiligings groep (NSG).

* [Virtuele netwerken en virtuele machines in azure](../virtual-machines/network-overview.md)

* [Overzicht van Azure Firewall](../firewall/overview.md)

* [Overzicht van de firewall van Azure Web Application](../web-application-firewall/overview.md)

* [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md)

* [Overzicht van Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)

* [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure Automation en gerelateerde resources te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources.

* [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy voor beeld van ingebouwde invoeg toepassingen voor Azure Automation](./policy-reference.md)

* [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Hulp**: de onderliggende virtuele machines of het besturings systeem van de multi tenant-runbook worker worden momenteel niet weer gegeven Azure Automation. Dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, gebruikt u Azure Security Center aanbeveling [beveiligings problemen in beveiligings configuraties op uw Virtual Machines oplossen] voor het onderhouden van beveiligings configuraties op uw virtuele machines.

* [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md)

* [Azure Security Center aanbevelingen herstellen](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Resource Manager sjablonen en Azure Policy voor het veilig configureren van Azure-resources die zijn gekoppeld aan Azure Automation. Azure Resource Manager sjablonen zijn JSON-bestanden die worden gebruikt voor het implementeren van Azure-resources en aangepaste sjablonen moeten worden opgeslagen en veilig worden bewaard in een code opslagplaats. Gebruik de functie integratie van bron beheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslag plaats voor bron beheer. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Integratie van bronbeheer gebruiken](./source-control-integration.md)

* [Informatie over het maken van Azure Resource Manager sjablonen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Wat zijn Azure Policy effecten?](../governance/policy/concepts/effects.md)

* [Een Automation-account implementeren met behulp van een Azure Resource Manager sjabloon](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Policy voor beeld van ingebouwde invoeg toepassingen voor Azure Automation](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Hulp**: Azure Automation maakt momenteel geen ondersteuning voor de onderliggende virtuele machines of het besturings systeem van een multi tenant-runbook Worker en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, zijn er verschillende opties voor het onderhouden van een veilige configuratie voor virtuele Azure-machines voor implementatie:

- Azure Resource Manager sjablonen: Dit zijn JSON-bestanden die worden gebruikt voor het implementeren van een VM vanuit de Azure Portal, en aangepaste sjabloon moet worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen.
- Aangepaste virtuele harde schijf (VHD): in sommige gevallen is het vereist dat er aangepaste VHD-bestanden worden gebruikt, zoals bij het verwerken van complexe omgevingen die niet op een andere manier kunnen worden beheerd.
- Azure Automation status configuratie: zodra het basis besturingssysteem is geïmplementeerd, kan dit worden gebruikt voor een gedetailleerdere controle van de instellingen en afgedwongen via het Automation-Framework.

Voor de meeste scenario's kan de micro soft-sjablonen voor virtuele machines die worden gecombineerd met de configuratie van de Azure Automation status, helpen bij de vergadering en het onderhouden van de beveiligings vereisten.

* [Informatie over het downloaden van de VM-sjabloon](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informatie over het maken van ARM-sjablonen](../virtual-machines/windows/ps-template.md)

* [Een aangepaste VM-VHD uploaden naar Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen en desired state Configuration-scripts. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory als deze zijn geïntegreerd met Azure DevOps, of Active Directory als deze zijn geïntegreerd met TFS. Gebruik de functie integratie van bron beheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslag plaats voor bron beheer.

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Integratie van bronbeheer gebruiken](./source-control-integration.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Hulp**: Azure Automation maakt momenteel geen ondersteuning voor de onderliggende virtuele machines of het besturings systeem van een multi tenant-runbook Worker en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, moet u ervoor zorgen dat u de toegang tot de aangepaste installatie kopie van het besturings systeem die zich in uw opslag account bevindt, goed beperkt zodat alleen gemachtigde gebruikers toegang hebben tot de installatie kopie.

* [Meer informatie over Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure-resources definiëren en implementeren met behulp van Azure Policy. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw specifieke resources.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Policy voor beeld van ingebouwde invoeg toepassingen voor Azure Automation](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Hulp**: Azure Automation maakt momenteel geen ondersteuning voor de onderliggende virtuele machines of het besturings systeem van een multi tenant-runbook Worker en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, gebruikt u Azure Automation status configuratie op Runbook workers die een Configuration Management-service voor desired state Configuration-knoop punten zijn in elke Cloud of on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](./automation-dsc-onboarding.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy om Azure-resource configuraties te Signa leren en te controleren, het beleid kan worden gebruikt voor het detecteren van een bepaalde resource die niet is geconfigureerd met een persoonlijk eind punt.

Wanneer u de functie Hybrid Runbook Worker gebruikt, maakt u gebruik van Azure Security Center om basislijn scans voor uw virtuele Azure-machines uit te voeren. Aanvullende methoden voor automatische configuratie bevatten de configuratie van de Azure Automation-status.

* [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Aan de slag met de configuratie van de Azure Automation-status](./automation-dsc-getting-started.md)

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy voor beeld van ingebouwde invoeg toepassingen voor Azure Automation](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: de Azure Automation aanbieding van de onderliggende virtuele machines of het besturings systeem van de multi tenant-runbook Worker is momenteel niet beschikbaar en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, gebruikt u Azure Automation status configuratie voor Runbook workers die een Configuration Management-service voor desired state Configuration-knoop punten zijn in elke Cloud of on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](./automation-dsc-onboarding.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Runbook-verificatie gebruiken met beheerde identiteiten](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Een Key Vault maken](../key-vault/general/quick-create-portal.md)

* [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

* [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten voor het leveren van Azure-Services met een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

* [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**: de Azure Automation aanbieding van de onderliggende virtuele machines of het besturings systeem van de multi tenant-runbook Worker is momenteel niet beschikbaar en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, gebruikt u micro soft anti-malware voor virtuele Azure Windows-machines om uw runbook worker bronnen voortdurend te controleren en te beschermen.

* [Micro soft anti-malware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing; Met Azure Automation als een service worden geen bestanden opgeslagen. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Automation), maar wordt niet uitgevoerd op uw inhoud.

* [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../security/fundamentals/antimalware.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Hulp**: Azure Automation maakt momenteel geen ondersteuning voor de onderliggende virtuele machines of het besturings systeem van een multi tenant-runbook Worker en dit wordt verwerkt door het platform. Dit besturings element is niet van toepassing als u gebruikmaakt van de out-of-the Box Service zonder Hybrid Runbook Workers.

Wanneer u de functie Hybrid Runbook Worker gebruikt, moet u micro soft antimalware voor Azure gebruiken om automatisch de nieuwste hand tekening, platform en engine-updates te installeren op uw runbook worker. Volg de aanbevelingen in Azure Security Center: "COMPUTE &amp; apps" om ervoor te zorgen dat alle eind punten up-to-date zijn met de nieuwste hand tekeningen. Het Windows-besturings systeem kan verder worden beveiligd met extra beveiliging om het risico van aanvallen op virussen of schadelijke software te beperken met behulp van de micro soft Defender Advanced Threat Protection-Service die kan worden geïntegreerd met Azure Security Center.

* [Micro soft antimalware implementeren voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: gebruik Azure Resource Manager om Azure Automation-accounts en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die als back-ups kunnen worden gebruikt om Azure Automation-accounts en gerelateerde resources te herstellen. Gebruik Azure Automation om de API van de Azure Resource Manager-sjabloon regel matig te kunnen aanroepen.

Gebruik de functie integratie van bron beheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslag plaats voor bron beheer.

* [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager-sjabloon verwijzing voor Azure Automation resources](/azure/templates/microsoft.automation/allversions)

* [Een Automation-account maken met behulp van een Azure Resource Manager sjabloon](./quickstart-create-automation-account-template.md)

* [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Resource groepen-sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

* [Inleiding tot Azure Automation](./automation-intro.md)

* [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Integratie van bronbeheer gebruiken](./source-control-integration.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik Azure Resource Manager om Azure Automation-accounts en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren die als back-ups kunnen worden gebruikt om Azure Automation-accounts en gerelateerde resources te herstellen. Gebruik Azure Automation om de API van de Azure Resource Manager-sjabloon regel matig te kunnen aanroepen. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault. U kunt uw runbooks exporteren naar script bestanden met behulp van Azure Portal of Power shell.

* [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager-sjabloon verwijzing voor Azure Automation resources](/azure/templates/microsoft.automation/allversions)

* [Een Automation-account maken met behulp van een Azure Resource Manager sjabloon](./quickstart-create-automation-account-template.md)

* [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Resource groepen-sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

* [Inleiding tot Azure Automation](./automation-intro.md)

* [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure data backup voor Automation-accounts](./automation-managing-data.md#data-backup)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u regel matig de implementatie van Azure Resource Manager sjablonen regel matig uitvoert op een geïsoleerd abonnement, indien nodig. Het herstellen van een back-up van door de klant beheerde sleutels testen.

* [Resources implementeren met ARM-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Gebruik van door de klant beheerde sleutels voor een Automation-account](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure DevOps gebruiken om uw code, zoals Azure Resource Manager sjablonen, veilig op te slaan en te beheren. Als u resources wilt beveiligen die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory als deze zijn geïntegreerd met Azure DevOps, of Active Directory als deze zijn geïntegreerd met TFS.

Gebruik de functie integratie van bron beheer om uw runbooks in uw Automation-account up-to-date te houden met scripts in uw opslag plaats voor bron beheer.

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Integratie van bronbeheer gebruiken](./source-control-integration.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

* [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

* [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

* [Continue export configureren](../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

* [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

* [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)