---
title: Azure-beveiligingsbasislijn voor Azure Machine Learning
description: De Azure Machine Learning beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816995"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure-beveiligingsbasislijn voor Azure Machine Learning

Met deze beveiligingsbasislijn worden richtlijnen van [Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Microsoft Azure Machine Learning. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Machine Learning. **Besturingselementen** die niet van Azure Machine Learning zijn uitgesloten.

 
Zie het volledige Azure Machine Learning toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Machine Learning volledig is toegewezen aan de Azure Security [Azure Machine Learning-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Azure Machine Learning is afhankelijk van andere Azure-services voor rekenbronnen. Rekenbronnen (rekendoelen) worden gebruikt om modellen te trainen en te implementeren. U kunt deze rekendoelen maken in een virtueel netwerk. U kunt bijvoorbeeld Azure Virtual Machine Learning compute-instantie gebruiken om een model te trainen en het model vervolgens te implementeren in Azure Kubernetes Service (AKS). U kunt uw machine learning beveiligen door Azure Machine Learning training en de deferentietaken binnen een virtueel Azure-netwerk te isoleren.

Azure Firewall kunnen worden gebruikt om de toegang tot uw Azure Machine Learning werkruimte en het openbare internet te beheren.

- [Overzicht van isolatie van virtuele netwerken en privacy](how-to-network-security-overview.md)

- [Werkruimte achter Azure Firewall gebruiken voor Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's bewaken en in een logboek plaatsen

**Richtlijnen:** Azure Machine Learning is afhankelijk van andere Azure-services voor rekenbronnen. Wijs netwerkbeveiligingsgroepen toe aan de netwerken die zijn gemaakt als Machine Learning implementatie. 

Schakel stroomlogboeken voor netwerkbeveiligingsgroep in en verzend de logboeken naar een Azure Storage voor controle. U kunt de stroomlogboeken ook verzenden naar een Log Analytics-werkruimte en vervolgens Traffic Analytics om inzicht te krijgen in verkeerspatronen in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren, hotspots en beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [Stroomlogboeken van netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geboden door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** u kunt HTTPS inschakelen om communicatie te beveiligen met webservices die zijn geïmplementeerd door Azure Machine Learning. Webservices worden geïmplementeerd in Azure Kubernetes Services (AKS) of Azure Container Instances (ACI) en beveiligen de gegevens die door clients worden verzonden. U kunt ook privé-IP-adressen gebruiken met AKS om scoren te beperken, zodat alleen clients achter een virtueel netwerk toegang hebben tot de webservice.

- [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)

- [Overzicht van isolatie en privacy van virtuele netwerken](how-to-network-security-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw Machine Learning-exemplaar om u te beschermen tegen gedistribueerde DDoS-aanvallen (Denial of Service). Gebruik Azure Security Center Geïntegreerde detectie van bedreigingen om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te detecteren.

Implementeer Azure Firewall netwerkgrenzen van de organisatie met filteren op basis van bedreigingsinformatie ingeschakeld en geconfigureerd op 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Werkruimte achter Azure Firewall gebruiken voor Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Voor meer informatie over de Azure Security Center detectie van bedreigingen](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** voor VM's met de juiste extensie die in uw Azure Machine Learning-services is geïnstalleerd, kunt u pakketopname Network Watcher om afwijkende activiteiten te onderzoeken. 

- [Een Network Watcher maken](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blokkeren.

Selecteer een aanbieding in Azure Marketplace ondersteuning biedt voor de functionaliteit van IDS/IPS met mogelijkheden voor nettoladinginspectie.  Wanneer payloadinspectie geen vereiste is, kunnen Azure Firewall bedreigingsinformatie worden gebruikt. Azure Firewall filteren op basis van bedreigingsinformatie wordt gebruikt om verkeer van en naar bekende schadelijke IP-adressen en domeinen te waarschuwen en/of te blokkeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** voor resources die toegang tot uw Azure Machine Learning-account nodig hebben, gebruikt u Virtual Network-servicetags voor het definiëren van netwerktoegangsbesturingselementen voor netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld AzureMachineLearning) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

Azure Machine Learning Service een lijst met servicetags voor de rekendoelen in een virtueel netwerk documenteert waarmee de complexiteit wordt geminimaliseerd, kunt u deze gebruiken als richtlijnen in uw netwerkbeheer.

- [Voor meer informatie over het gebruik van servicetags](../virtual-network/service-tags-overview.md)

- [Overzicht van isolatie en privacy van virtuele netwerken](how-to-network-security-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die zijn gekoppeld aan uw Azure Machine Learning-naamruimten met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten Microsoft.MachineLearning en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Machine Learning-naamruimten te controleren of af te dwingen. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** gebruik tags voor netwerkbronnen die zijn gekoppeld aan uw Azure Machine Learning implementatie om ze logisch te ordenen op basis van een taxonomie.

Voor een resource in uw Azure Machine Learning netwerk dat ondersteuning biedt voor het veld Beschrijving, gebruikt u deze om de regels te documenteren die verkeer van/naar een netwerk toestaan.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren voor netwerkresources met betrekking tot Azure Machine Learning. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen via Azure Monitor voor het samenvoegen van beveiligingsgegevens die zijn gegenereerd door Azure Machine Learning. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren, en gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag. U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM (Security Incident and Event Management) van derden.

- [Diagnostische logboeken configureren voor Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-resources in voor toegang tot audit-, beveiligings- en diagnostische logboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, doeladressen en andere nuttige elementen.

U kunt ook de Machine Learning service voor beveiligings- en nalevingsdoeleinden correleren.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Logboekregistratie en verschillende logboektypen in Azure begrijpen](/azure/azure-monitor/platform/platform-logs-overview)

- [Logboekregistratie inschakelen in Azure Machine Learning](how-to-log-view-metrics.md)

- [Bewakings- Azure Machine Learning](monitor-azure-machine-learning.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor het verzamelen en bewaken ervan. 

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Gebruik voor alle rekenbronnen die eigendom zijn van uw organisatie, Azure Security Center besturingssysteem te bewaken. 

- [Interne hostlogboeken van virtuele Azure-machines verzamelen met Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Inzicht Azure Security Center het verzamelen van gegevens](../security-center/security-center-enable-data-collection.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor logboekretentieperiode in voor Log Analytics-werkruimten die zijn gekoppeld aan uw Azure Machine Learning-instanties overeenkomstig de nalevingsvoorschriften van uw organisatie.

- [Retentieparameters voor logboeken instellen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer logboeken op afwijkende gedrag en controleer regelmatig de resultaten van uw Azure Machine Learning. Gebruik Azure Monitor en een Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens.

U kunt ook gegevens inschakelen en inschakelen voor Azure Sentinel siem van derden. 

- [Query's uitvoeren voor Azure Machine Learning in Log Analytics-werkruimten](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Logboekregistratie inschakelen Azure Machine Learning](how-to-log-view-metrics.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics-query's](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** configureer in Azure Monitor logboeken met betrekking tot Azure Machine Learning in het activiteitenlogboek en Machine Learning diagnostische instellingen voor het verzenden van logboeken naar een Log Analytics-werkruimte voor query's of naar een opslagaccount voor langetermijnarchivering. Gebruik de Log Analytics-werkruimte om waarschuwingen te maken voor afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen.

U kunt ook gegevens inschakelen en in gebruik nemen om Azure Sentinel.

- [Voor meer informatie over Azure Machine Learning waarschuwingen](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Waarschuwingen voor logboekgegevens van Log Analytics-werkruimten](/azure/azure-monitor/learn/tutorial-response)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor de antimalware-implementatie van Azure Machine Learning Service.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenresources die eigendom zijn van uw organisatie, moet u het verzamelen van antimalwaregebeurtenissen inschakelen Microsoft Antimalware voor Azure Cloud Services en Virtual Machines.

- [De extensie Microsoft Antimalware cloudservices configureren](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Inzicht Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Niet van toepassing; Azure Machine Learning verwerkt of produceert geen DNS-gerelateerde logboeken.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Controlelogregistratie via opdrachtregel inschakelen

**Richtlijnen:** Azure Machine Learning bieden verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Security Center voor het inschakelen van bewaking van beveiligingsgebeurtenislogboek voor virtuele Azure-machines. Azure Security Center log analytics-agent inrichten op alle ondersteunde Virtuele Azure-VM's en op nieuwe VM's die worden gemaakt als automatische inrichting is ingeschakeld. U kunt de agent ook handmatig installeren. De agent schakelt gebeurtenis 4688 voor het maken van het proces en het opdrachtregelveld in gebeurtenis 4688 in. Nieuwe processen die op de VM zijn gemaakt, worden vastgelegd in het gebeurtenislogboek en bewaakt door Security Center detectieservices van de VM.

- [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** u kunt het tabblad Identiteits- en toegangsbeheer voor een resource in de Azure Portal gebruiken om op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) te configureren en de inventaris van uw resources Azure Machine Learning onderhouden. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory. U kunt ingebouwde rollen of aangepaste rollen gebruiken voor personen en groepen.

Azure Machine Learning biedt ingebouwde rollen voor algemene beheerscenario's in Azure Machine Learning. Een persoon met een profiel in Azure Active Directory (Azure AD) kan deze rollen toewijzen aan gebruikers, groepen, service-principals of beheerde identiteiten om toegang te verlenen tot resources en bewerkingen op Azure Machine Learning resources.

U kunt ook de Azure AD PowerShell-module gebruiken om ad-hoc-query's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Op rollen gebaseerd toegangsbeheer in Azure in Azure Machine Learning](how-to-assign-roles.md)

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Toegangsbeheer Machine Learning resources wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaardwachtwoorden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Azure Machine Learning worden geleverd met drie standaardrollen wanneer een nieuwe werkruimte wordt gemaakt. Er worden standaardbesturingssysteemprocedures voor het gebruik van eigenaarsaccounts gemaakt.

U kunt ook Just-In-Time-toegang tot beheerdersaccounts inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management en Azure Resource Manager.

- [Meer informatie over Machine Learning standaardrollen](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Meer informatie over Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Eenmalige aanmelding (SSO) gebruiken met Azure Active Directory

**Richtlijnen:** Machine Learning is geïntegreerd met Azure Active Directory (Azure AD), gebruikt u eenmalige aanmelding van Azure AD in plaats van afzonderlijke, afzonderlijke referenties per service te configureren. Gebruik Azure Security Center identiteits- en toegangsaanbevelingen.

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en de aanbevelingen Azure Security Center identiteit en toegang volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Gebruik een beveiligd, door Azure beheerd werkstation (ook wel een Privileged Access Workstation of PAW genoemd) voor beheertaken waarvoor verhoogde bevoegdheden zijn vereist.

- [Inzicht krijgen in beveiligde, door Azure beheerde werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie Azure Active Directory (Azure AD) inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten en -bewaking om te detecteren wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Alleen Azure-resources beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) benoemde locaties om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties in Azure AD configureren](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.
 
Roltoegang kan worden beperkt tot meerdere niveaus in Azure. Voor Machine Learning kunnen rollen bijvoorbeeld worden beheerd op werkruimteniveau. Als u bijvoorbeeld eigenaarstoegang hebt tot een werkruimte, hebt u mogelijk geen eigenaarstoegang tot de resourcegroep die de werkruimte bevat. Dit biedt gedetailleerdere besturingselementen voor toegang om rollen binnen dezelfde resourcegroep te scheiden. 

- [De toegang tot een Azure Machine Learning-werkruimte beheren](how-to-assign-roles.md) 
 
- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik daarnaast Azure AD-identiteits- en toegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben.

Gebruik Azure AD Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Implementatie Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met elk SIEM-/bewakingshulpprogramma.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren met Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection-functies om geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten te configureren. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** Niet van toepassing; Azure Machine Learning Service biedt geen ondersteuning voor klanten-lockbox.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.
 
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Isolatie implementeren met behulp van afzonderlijke abonnementen en beheergroepen voor afzonderlijke beveiligingsdomeinen, zoals omgevingstype en gegevensgevoeligheidsniveau. U kunt het toegangsniveau beperken tot uw Azure-resources die uw toepassingen en bedrijfsomgevingen nodig hebben. U kunt de toegang tot Azure-resources beheren via Azure RBAC.
 
- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

 
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** Gebruik een externe oplossing van Azure Marketplace in netwerkperimeters om te controleren op niet-geautoriseerde overdracht van gevoelige informatie en om dergelijke overdrachten te blokkeren tijdens het waarschuwen van informatiebeveiligingsprofessionals. 

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** webservices die zijn geïmplementeerd via Azure Machine Learning bieden alleen ondersteuning voor TLS-versie 1.2, die gegevensversleuteling tijdens overdracht afdwingt.

- [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure Machine Learning. Implementeert zo nodig een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het veel moeite doen om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Azure Machine Learning ondersteuning voor het gebruik Azure Active Directory (Azure AD) om aanvragen voor Machine Learning resources. Met Azure AD kunt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken om machtigingen te verlenen aan een beveiligingsprincipaal( dit kan een gebruiker zijn of een toepassingsservice-principal).

- [De toegang tot een Azure Machine Learning-werkruimte beheren](how-to-assign-roles.md)

- [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Azure Machine Learning momentopnamen, uitvoer en logboeken opgeslagen in het Azure Blob Storage-account dat is gekoppeld aan Azure Machine Learning werkruimte en uw abonnement. Alle gegevens die zijn opgeslagen in Azure Blob Storage, worden in rust versleuteld met door Microsoft beheerde sleutels. U kunt gegevens die zijn opgeslagen in Azure Blob Storage ook versleutelen met uw eigen sleutels in Machine Learning service. 

- [Azure Machine Learning data-at-rest versleutelen](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

- [Door de klant beheerde versleutelingssleutels configureren](../storage/common/customer-managed-keys-configure-key-vault.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer wijzigingen plaatsvinden in productie-exemplaren van Azure Machine Learning en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor het vulnerability management van Azure Machine Learning Service. 

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenresources die eigendom zijn van uw organisatie, volgt u de aanbevelingen van Azure Security Center voor het uitvoeren van evaluaties van beveiligingsprobleem op uw virtuele Azure-machines, containerafbeeldingen en SQL-servers.

- [Aanbevelingen voor het Azure Security Center van beveiligingsleeds implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Een geautomatiseerde beheeroplossing voor besturingssysteempatchs implementeren

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor het patchbeheer van Azure Machine Learning Service. 

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Gebruik voor alle rekenresources die eigendom zijn van uw organisatie, Azure Automation Updatebeheer om ervoor te zorgen dat de meest recente beveiligingsupdates worden geïnstalleerd op uw Windows- en Linux-VM's. Zorg ervoor dat Windows Update windows-VM's is ingeschakeld en zo is ingesteld dat deze automatisch worden bijgewerkt.

- [Een Updatebeheer voor virtuele machines in Azure configureren](../automation/update-management/overview.md)

- [Inzicht in Azure-beveiligingsbeleid dat wordt bewaakt door Security Center](../security-center/policy-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een geautomatiseerde oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** Azure Machine Learning ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Gebruik een oplossing voor patchbeheer van derden voor rekenbronnen die eigendom zijn van uw organisatie. Klanten die al Configuration Manager in hun omgeving kunnen ook gebruikmaken van System Center Updates Publisher, zodat ze aangepaste updates kunnen publiceren in Windows Server Update Service. Hierdoor kunnen Updatebeheer machines patchen die gebruikmaken van Configuration Manager hun updateopslagplaats met software van derden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Azure Machine Learning bieden verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenresources die eigendom zijn van uw organisatie, volgt u de aanbevelingen van Azure Security Center voor het uitvoeren van evaluaties van beveiligingsprobleem op uw virtuele Azure-machines, containerafbeeldingen en SQL-servers. Exporteer scanresultaten met consistente intervallen en vergelijk de resultaten met eerdere scans om te controleren of beveiligingsproblemen zijn vereengeld. Wanneer u vulnerability management aanbevelingen gebruikt die door Azure Security Center worden voorgesteld, kunt u naar de portal van de geselecteerde oplossing draaien om historische scangegevens weer te geven.

- [Aanbevelingen voor de evaluatie Azure Security Center beveiligingsprobleem implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen en ontdekken van resources (zoals rekenkracht, opslag, netwerk, poorten en protocollen enzovoort) in uw abonnementen. Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Azure Resource Graph Explorer, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources, metagegevens toevoegen om logisch te organiseren volgens een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

- [ Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [ Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)
 
- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Gebruik daarnaast de Azure Resource Graph om resources in de abonnementen op te vragen/te ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** Azure Machine Learning bieden verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Automation Wijzigingen bijhouden en inventaris het verzamelen van inventarisgegevens van uw Windows- en Linux-VM's te automatiseren. De softwarenaam, versie, uitgever en vernieuwingstijd zijn beschikbaar via de Azure Portal. Als u de installatiedatum van de software en andere informatie wilt weten, moet u diagnostische gegevens op gastniveau inschakelen en de Windows-gebeurtenislogboeken doorvertalen naar de Log Analytics-werkruimte.

- [Een inventarisverzameling Azure Automation een VM inschakelen](../automation/automation-tutorial-installed-software.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Azure Machine Learning bieden verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Security Center FiM (File Integrity Monitoring) van de Azure Security Center om alle software te identificeren die op VM's is geïnstalleerd. Een andere optie die kan worden gebruikt in plaats van of in combinatie met FIM, is Azure Automation Wijzigingen bijhouden en inventaris inventaris te verzamelen van uw Linux- en Windows-VM's. 

U kunt uw eigen proces implementeren voor het verwijderen van niet-geautoriseerde software. U kunt ook een oplossing van derden gebruiken om niet-goedgekeurde software te identificeren.

Verwijder Azure-resources wanneer deze ze niet meer nodig zijn.

- [Bewaking van bestandsintegriteit gebruiken](../security-center/security-center-file-integrity-monitoring.md)

- [Meer Azure Automation Wijzigingen bijhouden en inventaris](../automation/change-tracking/overview.md)

- [Inventaris van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

- [Azure-resourcegroep en -resource verwijderen](../azure-resource-manager/management/delete-resource-group.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Azure Machine Learning ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Gebruik voor rekenresources die eigendom zijn van uw organisatie Azure Security Center adaptieve toepassingsregelaars om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en dat alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

- [Adaptieve toepassingsbesturingselementen Azure Security Center gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Gebruik daarnaast de Azure Resource Graph om resources in de abonnementen op te vragen en te ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Azure Machine Learning ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Gebruik voor alle rekenresources die eigendom zijn van uw organisatie Azure Security Center adaptieve toepassingsbesturingselementen om op te geven op welke bestandstypen een regel al dan niet van toepassing is.

Implementeert een oplossing van derden als adaptieve toepassingsbesturingselementen niet aan de vereiste voldoen.

- [Adaptieve toepassingsbesturingselementen Azure Security Center gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) om de mogelijkheid van gebruikers om te communiceren met Azure Resources Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren in rekenbronnen beperken

**Richtlijnen:** Azure Machine Learning bieden verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenresources die eigendom zijn van uw organisatie, kunt u, afhankelijk van het type scripts, besturingssysteemspecifieke configuraties of resources van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren in Azure Compute-resources te beperken. U kunt ook Azure Security Center adaptieve toepassingsregelaars gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en dat alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

- [De uitvoering van PowerShell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Adaptieve toepassingsbesturingselementen Azure Security Center gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Machine Learning Service met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.MachineLearning om aangepaste beleidsregels te maken voor het controleren of afdwingen van de configuratie van uw Azure Machine Learning services.

Azure Resource Manager heeft de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie.

U kunt ook de aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

Azure Machine Learning biedt volledige ondersteuning voor Git-opslagplaatsen voor het bijhouden van werk; U kunt opslagplaatsen rechtstreeks naar uw bestandssysteem voor gedeelde werkruimten klonen, Git op uw lokale werkstation gebruiken en ervoor zorgen dat beveiligde configuraties van toepassing zijn op codebronnen als onderdeel van uw Machine Learning-omgeving.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Configuraties van beveiligde besturingssystemen tot stand

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor beveiligde besturingssysteemconfiguraties van Azure Machine Learning Service.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Security Center om beveiligingsconfiguraties te onderhouden voor alle rekenbronnen.  Daarnaast kunt u aangepaste installatie afbeeldingen van besturingssystemen of Azure Automation State Configuration gebruiken om de beveiligingsconfiguratie van het besturingssysteem vast te stellen dat uw organisatie vereist.

- [Aanbevelingen voor Azure Security Center bewaken](../security-center/security-center-recommendations.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration Overzicht](../automation/automation-dsc-overview.md)

- [Een VHD uploaden en deze gebruiken om nieuwe Windows-VM's te maken in Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Een linux-VM maken op een aangepaste schijf met de Azure CLI](../virtual-machines/linux/upload-vhd.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Azure-resources. Daarnaast kunt u de sjablonen Azure Resource Manager om de beveiligingsconfiguratie te onderhouden van uw Azure-resources die uw organisatie nodig heeft. 
 
- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)
 
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor beveiligde besturingssysteemconfiguraties van Azure Machine Learning Service.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, volgt u de aanbevelingen van Azure Security Center het uitvoeren van evaluaties van beveiligingsleeds op uw Azure-rekenbronnen.  Daarnaast kunt u sjablonen, aangepaste Azure Resource Manager of Azure Automation State Configuration gebruiken om de beveiligingsconfiguratie van het besturingssysteem te onderhouden die uw organisatie nodig heeft.   De virtuele-machinesjablonen van Microsoft in combinatie met Azure Automation State Configuration kunnen helpen bij het voldoen aan en onderhouden van de beveiligingsvereisten. 

Houd er rekening Azure Marketplace dat de door Microsoft gepubliceerde virtuele machines worden beheerd en onderhouden door Microsoft. 

- [Aanbevelingen voor de evaluatie Azure Security Center beveiligingsprobleem implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

- [Een virtuele Azure-machine maken op basis van een ARM-sjabloon](../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration overzicht](../automation/automation-dsc-overview.md)

- [Een virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md)

- [Informatie over het downloaden van de VM-sjabloon](/previous-versions/azure/virtual-machines/windows/download-template)

- [Voorbeeld van een script voor het uploaden van een VHD naar Azure om een nieuwe VM te maken](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt voor uw Machine Learning of gerelateerde resources, gebruikt u Azure-repos om uw code veilig op te slaan en te beheren.

Azure Machine Learning biedt volledige ondersteuning voor Git-opslagplaatsen voor het bijhouden van werk; U kunt opslagplaatsen rechtstreeks naar uw bestandssysteem voor gedeelde werkruimten klonen, Git op uw lokale werkstation gebruiken en ervoor zorgen dat beveiligde configuraties van toepassing zijn op codebronnen als onderdeel van uw Machine Learning-omgeving.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste besturingssysteeminstallatie afbeeldingen

**Richtlijnen:** Azure Machine Learning ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenresources die eigendom zijn van uw organisatie, gebruikt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om ervoor te zorgen dat alleen gemachtigde gebruikers toegang hebben tot uw aangepaste afbeeldingen. Gebruik een Azure Shared Image Gallery u uw afbeeldingen kunt delen met verschillende gebruikers, service-principals of Azure Active Directory (Azure AD)-groepen binnen uw organisatie. Sla containerafbeeldingen op in Azure Container Registry en gebruik Azure RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben.

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Inzicht in Azure RBAC voor Container Registry](../container-registry/container-registry-roles.md)

- [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Shared Image Gallery overzicht](../virtual-machines/shared-image-galleries.md)

- [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)

**Verantwoordelijkheid:** Niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy-aliassen in de naamruimte Microsoft.MachineLearning om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Als de rekenresource eigendom is van Microsoft, is Microsoft verantwoordelijk voor de implementatie van beveiligde configuratie van Azure Machine Learning Service.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Automation State Configuration voor Desired State Configuration-knooppunten (DSC) in een cloud- of on-premises datacenter. U kunt eenvoudig machines onboarden, declaratieve configuraties toewijzen en rapporten weergeven waarin wordt weergegeven dat elke machine voldoet aan de gewenste status die u hebt opgegeven. 

- [Het inschakelen van Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure-resources. Gebruik daarnaast Azure Policy om Azure-resourceconfiguraties te waarschuwen en te controleren.
 
 
 
- [ Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** Als de rekenresource het eigendom is van Microsoft, is Microsoft verantwoordelijk voor het geautomatiseerd controleren van beveiligde configuratie van Azure Machine Learning Service.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, gebruikt u Azure Security Center Compute-apps en volgt u de aanbevelingen voor VM's &amp; en servers en containers.

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik beheerde identiteiten in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen.

Azure Machine Learning ondersteuning biedt voor versleuteling van gegevensopslag met door de klant beheerde sleutels, moet u het roteren en intrekken van sleutels beheren volgens de beveiligings- en nalevingsvereisten van de organisatie. 

Gebruik Azure Key Vault geheimen veilig door te geven aan externe runs in plaats van cleartext in uw trainingsscripts.

- [Azure Machine Learning door de klant beheerde sleutels](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Verificatiereferentiegeheimen gebruiken in Azure Machine Learning trainings runs](how-to-use-secrets-in-runs.md)

- [Beheerde identiteiten gebruiken voor Azure-resources](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault](../key-vault/general/quick-create-portal.md)

- [Verificatie bij Key Vault](../key-vault/general/authentication.md)

- [Een toegangsbeleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Azure Machine Learning ondersteuning voor zowel ingebouwde rollen als de mogelijkheid om aangepaste rollen te maken. Gebruik beheerde identiteiten om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code.

- [De toegang tot een Azure Machine Learning-werkruimte beheren](how-to-assign-roles.md)

- [Beheerde identiteiten configureren voor Azure-resources](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Centraal beheerde antimalwaresoftware gebruiken

**Richtlijnen:** Antimalware van Microsoft is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Machine Learning), maar wordt niet uitgevoerd op klantinhoud.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor rekenbronnen die eigendom zijn van uw organisatie, kunt u Microsoft Antimalware azure gebruiken om uw resources continu te bewaken en te beschermen. Voor Linux gebruikt u een antimalwareoplossing van derden. Gebruik ook de Azure Security Center van gegevensservices om malware te detecteren die is geüpload naar opslagaccounts.

- [Een Microsoft Antimalware voor Azure configureren](../security/fundamentals/antimalware.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden die moeten worden geüpload naar niet-compute Azure-resources vooraf scannen

**Richtlijnen:** Microsoft Antimalware is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Machine Learning), maar wordt niet uitgevoerd op klantinhoud.

Het is uw verantwoordelijkheid om alle inhoud die wordt geüpload naar niet-compute Azure-resources vooraf te scannen. Microsoft heeft geen toegang tot klantgegevens en kan daarom namens u geen antimalwarescans van klantinhoud uitvoeren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antimalwaresoftware en handtekeningen worden bijgewerkt

**Richtlijnen:** Antimalware van Microsoft wordt ingeschakeld en onderhouden voor de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Machine Learning), maar wordt niet uitgevoerd op klantinhoud.

Azure Machine Learning biedt verschillende ondersteuning voor verschillende rekenbronnen en zelfs uw eigen rekenbronnen. Voor alle rekenbronnen die eigendom zijn van uw organisatie, volgt u de aanbevelingen in Azure Security Center, Compute Apps om ervoor te zorgen dat alle eindpunten zijn bijgewerkt met de &amp; nieuwste handtekeningen. Voor Linux gebruikt u een antimalwareoplossing van derden.

- [Implementatie van Microsoft Antimalware voor Azure](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Gegevensherstelbeheer in Machine Learning service gebeurt via gegevensbeheer in verbonden gegevensopslag. Zorg ervoor dat u de richtlijnen voor gegevensherstel volgt voor verbonden winkels om een back-up te maken van gegevens volgens het beleid van de klantorganisatie.

- [Bestanden herstellen vanuit een back-up van virtuele Azure-machines](../backup/backup-azure-restore-files-from-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Gegevensback-up in Machine Learning service gebeurt via gegevensbeheer in verbonden gegevensopslag. Schakel Azure Backup VM's in en configureer de gewenste frequentie en bewaarperioden. Back-up maken van door de klant beheerde sleutels in Azure Key Vault.

- [Bestanden herstellen vanuit een back-up van virtuele Azure-machines](../backup/backup-azure-restore-files-from-vm.md)

- [Sleutels voor Key Vault herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Validatie van gegevensback-Machine Learning service wordt uitgevoerd via gegevensbeheer in verbonden gegevensopslag. Periodiek gegevensherstel van inhoud in Azure Backup. Zorg ervoor dat u back-up van door de klant beheerde sleutels kunt herstellen.

- [Bestanden herstellen vanuit een back-up van een virtuele Azure-machine](../backup/backup-azure-restore-files-from-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** voor on-premises back-ups wordt versleuteling-at-rest geboden met behulp van de wachtwoordzin die u op voorwaarde dat u een back-up maakt naar Azure. Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om back-ups en door de klant beheerde sleutels te beveiligen. 

Schakel de beveiliging voor zacht verwijderen en ops manier in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van back-ups, moet u de functie voor zacht verwijderen inschakelen om uw gegevens op te slaan en te herstellen wanneer blobs of blob-momentopnamen worden verwijderd.

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

- [Voorlopig verwijderen en bescherming tegen opschonen in Azure Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md)

- [Zacht verwijderen voor Azure Blob-opslag](../storage/blobs/soft-delete-blob-overview.md)

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

**Richtlijnen:** Azure Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analytische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast kunt u abonnementen markeren met behulp van tags en een naamgevingssysteem maken om Azure-resources te identificeren en categoriseren, met name azure-resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de kritiekheid van de Azure-resources en de omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Identificeer zwakke punten en hiaten en pas uw reactieplan naar behoefte aan.

- [Publicatie van NIST- Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie voor continue export om risico's voor Azure-resources te identificeren. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of continu exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** Gebruik de functie werkstroomautomatisering Azure Security Center automatisch reacties op beveiligingswaarschuwingen en aanbevelingen te activeren om uw Azure-resources te beveiligen.

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
