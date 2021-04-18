---
title: Azure-beveiligingsbasislijn voor Container Instances
description: De Container Instances beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598785"
---
# <a name="azure-security-baseline-for-container-instances"></a>Azure-beveiligingsbasislijn voor Container Instances

Deze beveiligingsbasislijn past richtlijnen van [azure Security Benchmark versie 1.0 toe op](../security/benchmarks/overview-v1.md) Container Instances. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security-benchmark en de gerelateerde richtlijnen die van toepassing zijn op Container Instances. **Besturingselementen** die niet van Container Instances of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten.

Zie het volledige Container Instances toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Container Instances volledig is toegewezen aan de Azure Security [Container Instances-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Integreer uw containergroepen in Azure Container Instances met een virtueel Azure-netwerk.  Met virtuele Netwerken van Azure kunt u veel van uw Azure-resources, zoals containergroepen, in een routeerbaar netwerk zonder internet plaatsen.

Uitgaande netwerktoegang vanaf een subnet dat is gedelegeerd naar Azure Container Instances met behulp van Azure Firewall. 

- [Containerinstanties implementeren in een virtueel Azure-netwerk](/azure/container-instances/container-instances-vnet)

- [Het implementeren en configureren van Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om uw netwerkbronnen in Azure te beveiligen. Schakel NSG-stroomlogboeken in en verzend logboeken naar een opslagaccount voor verkeerscontrole. U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen. 

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md) 

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Niet van toepassing. Benchmark is bedoeld voor Azure App Service of rekenbronnen die webtoepassingen hosten.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** DDoS Standard-beveiliging op uw virtuele netwerken inschakelen voor beveiliging tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.  Implementeer Azure Firewall aan elk van de netwerkgrenzen van de organisatie met Bedreigingsinformatie ingeschakeld en geconfigureerd voor 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

U kunt Azure Security Center Just-In-Time-netwerktoegang gebruiken om NSG's te configureren om de blootstelling van eindpunten aan goedgekeurde IP-adressen voor een beperkte periode te beperken. Gebruik ook Azure Security Center adaptieve netwerkharding om NSG-configuraties aan te bevelen die poorten en bron-IP's beperken op basis van werkelijk verkeer en bedreigingsinformatie.

- [DDoS-beveiliging configureren](/azure/virtual-network/manage-ddos-protection)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

- [Inzicht Azure Security Center adaptieve netwerkharding](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center Just-In-Time-Access Control](../security-center/security-center-just-in-time.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, kunt u NSG-stroomlogboeken (Netwerkbeveiligingsgroep) inschakelen voor de NSG die is gekoppeld aan het subnet dat wordt gebruikt om uw Azure-containerregister te beveiligen. U kunt de NSG-stroomlogboeken registreren in een Azure Storage-account om stroomrecords te genereren. Indien nodig voor het onderzoeken van afwijkende activiteiten, moet u Azure Network Watcher pakketopname inschakelen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** selecteer een aanbieding in de Azure Marketplace ondersteuning biedt voor IDS-/IPS-functionaliteit met mogelijkheden voor nettoladinginspectie. Als inbraakdetectie en/of -preventie op basis van payloadinspectie geen vereiste is, kan Azure Firewall met bedreigingsinformatie worden gebruikt. Azure Firewall filteren op basis van bedreigingsinformatie kan verkeer van en naar bekende schadelijke IP-adressen en domeinen waarschuwen en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

- [Implementeren in een virtueel netwerk - Azure Container Instances](container-instances-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Niet van toepassing. Benchmark is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, gebruikt u voor resources die toegang tot uw containerregister nodig hebben servicetags voor virtuele netwerken voor de Azure Container Registry-service om netwerktoegangsbesturingselementen voor netwerkbeveiligingsgroepen of -Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetagnaam 'AzureContainerRegistry' op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres-voorvoegsels die door de servicetag worden omvat en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

- [Toegang via servicetag toestaan](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** wanneer u Azure Container Registry met Azure Container Instances, wordt u aangeraden standaardbeveiligingsconfiguraties te definiëren en te implementeren voor netwerkresources die zijn gekoppeld aan uw Azure-containerregister.

Gebruik Azure Policy aliassen in de naamruimten **Microsoft.ContainerRegistry** en **Microsoft.Network** om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw containerregisters te controleren of af te dwingen.

U kunt Azure Blueprints om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Manager-sjablonen, Azure RBAC-besturingselementen en beleidsdefinities, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen en de controle en het beheer afstemmen via versiebeheer.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** de klant kan Azure Blueprints gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Manager-sjablonen, Azure RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen en de controle en het beheer afstemmen via versiebeheer.

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren voor netwerkresources die betrekking hebben op uw containerregisters. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen via Azure Monitor voor het samenvoegen van beveiligingsgegevens die zijn gegenereerd door een Azure-containerin exemplaar. Gebruik Azure Monitor Log Analytics-werkruimte om query's uit te voeren en analyses uit te voeren, en gebruik Azure Storage accounts voor langetermijn-/archiveringsopslag.

- [Logboekregistratie van containergroep en exemplaar met Azure Monitor logboeken](container-instances-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie inschakelen voor Azure-resources

**Richtlijnen:** Azure Monitor verzamelt resourcelogboeken (voorheen diagnostische logboeken genoemd) voor door gebruikers gestuurde gebeurtenissen. Verzamel en gebruik deze gegevens om containerverificatiegebeurtenissen te controleren en een volledig activiteitentrail te bieden voor artefacten zoals pull- en pushgebeurtenissen, zodat u beveiligingsproblemen met uw containergroep kunt vaststellen.

- [Logboekregistratie van containergroep en exemplaar met Azure Monitor logboeken](container-instances-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Azure Monitor de retentieperiode van uw Log Analytics-werkruimte in op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage accounts voor opslag op lange termijn/archivering.

- [Logboekretentieparameters instellen voor Log Analytics-werkruimten](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer Azure Container Instances op afwijkende gedrag en controleer regelmatig de resultaten. Gebruik Azure Monitor Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens.

- [Inzicht in Log Analytics-werkruimte](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Een containergroep met logboekfunctie maken en logboeken opvragen](container-instances-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, gebruikt u de Azure Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen met betrekking tot uw Azure-containerregister.

- [Azure Container Registry voor diagnostische evaluatie en controle](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Waarschuwingen voor logboekgegevens van Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** Niet van toepassing. Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, verwerkt of produceert Azure Container Registry geen antimalwarelogboeken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Niet van toepassing. Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, is Azure Container Registry een eindpunt dat geen communicatie initieert en de service geen query's uitvoert op DNS.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en die kunnen worden opgevraagd. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te vinden die lid zijn van beheergroepen.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, moet u voor elk Azure-containerregister bijhouden of het ingebouwde beheerdersaccount is ingeschakeld of uitgeschakeld. Schakel het account uit wanneer het niet in gebruik is.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Container Registry beheerdersaccount](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Active Directory (Azure AD) heeft niet het concept van standaardwachtwoorden. Andere Azure-resources waarvoor een wachtwoord is vereist, dwingen af dat een wachtwoord wordt gemaakt met complexiteitsvereisten en een minimale wachtwoordlengte, die afhankelijk van de service verschillen. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die mogelijk gebruikmaken van standaardwachtwoorden.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, en het standaardbeheerdersaccount van een Azure-containerregister is ingeschakeld, worden complexe wachtwoorden automatisch gemaakt en moeten ze worden geroteerd. Schakel het account uit wanneer het niet in gebruik is.

- [Azure Container Registry beheerdersaccount](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, maakt u procedures voor het inschakelen van het ingebouwde beheerdersaccount van een containerregister. Schakel het account uit wanneer het niet in gebruik is.

- [Inzicht Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Azure Container Registry beheerdersaccount](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richtlijnen:** gebruik waar mogelijk eenmalige Azure Active Directory (Azure AD) in plaats van afzonderlijke afzonderlijke referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, gebruikt u voor afzonderlijke toegang tot het containerregister afzonderlijke aanmeldingen die zijn geïntegreerde met Azure AD.

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Afzonderlijke aanmelding bij een containerregister](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** GebruikWS (privileged access workstations) met meervoudige verificatie die is geconfigureerd voor aanmelden bij en configureren van Azure-resources.

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsbronnen voor activiteiten, controlegebeurtenissen en risicogebeurtenissen, waarmee u kunt integreren met elk Security Information and Event Management(SIEM)-/bewakingshulpprogramma.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt de gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Risk and Identity Protection-functies om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten.

- [Riskante aanmeldingen van Azure AD weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** Niet beschikbaar; Klanten-lockbox momenteel niet ondersteund voor Azure Container Instances.

- [Lijst met Klanten-lockbox ondersteunde services](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Resourcetags gebruiken om Te helpen bij het bijhouden van Azure-containerregisters die gevoelige informatie opslaan of verwerken.

Tag en versie container-afbeeldingen of andere artefacten in een register en vergrendel afbeeldingen of opslagplaatsen om te helpen bij het bijhouden van afbeeldingen die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Aanbevelingen voor het taggen en versieren van containerafbeeldingen](../container-registry/container-registry-image-tag-version.md)

- [Een containerafbeelding vergrendelen in een Azure-containerregister](../container-registry/container-registry-image-lock.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke containerregisters, abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende worden geïsoleerd.

Resources moeten worden gescheiden door een virtueel netwerk of subnet, op de juiste wijze worden gelabeld en beveiligd door een netwerkbeveiligingsgroep (NSG) of Azure Firewall.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Toegang tot een Azure-containerregister beperken met behulp van een virtueel Azure-netwerk of firewallregels](../container-registry/container-registry-vnet.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen of waarschuwingen configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** Implementeer een geautomatiseerd hulpprogramma op netwerkperimeters dat controleert op niet-geautoriseerde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantgegevens als gevoelig en gaat microsoft alles in het werk om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** zorg ervoor dat clients die verbinding maken met uw Azure Container Registry kunnen onderhandelen over TLS 1.2 of hoger. Microsoft Azure resources onderhandelen standaard over TLS 1.2.

Volg Azure Security Center aanbevelingen voor versleuteling in rust en versleuteling tijdens overdracht, indien van toepassing.

- [Inzicht in versleuteling tijdens overdracht met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, zijn de functies voor gegevensidentificatie, classificatie en preventie van verlies nog niet beschikbaar voor Azure Container Registry. Implementeert, indien nodig, een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantgegevens als gevoelig en gaat microsoft alles in het werk om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Implementatiegegevens versleutelen met Azure Container Instances](container-instances-encrypt-data.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, gebruikt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om de toegang tot gegevens en resources in een Azure-containerregister te beheren.

- [Azure RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry en machtigingen](../container-registry/container-registry-roles.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Indien vereist voor naleving van rekenbronnen, implementeert u een hulpprogramma van derden, zoals een geautomatiseerde oplossing voor preventie van gegevensverlies op basis van een host, om toegangsbesturingselementen voor gegevens af te dwingen, zelfs wanneer gegevens van een systeem worden gekopieerd.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantgegevens als gevoelig en gaat microsoft alles in het werk om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Versleuteling-at-rest gebruiken voor alle Azure-resources. Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, worden alle gegevens in een Azure-containerregister standaard in rust versleuteld met behulp van door Microsoft beheerde sleutels.

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

- [Door de klant beheerde sleutels in Azure Container Registry](https://aka.ms/acr/cmk)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** Log Analytics-werkruimten bieden een centrale locatie voor het opslaan en opvragen van logboekgegevens, niet alleen vanuit Azure-resources, maar ook on-premises resources en resources in andere clouds. Azure Container Instances bevat ingebouwde ondersteuning voor het verzenden van logboeken en gebeurtenisgegevens naar Azure Monitor logboeken.

- [Logboekregistratie van containergroep en exemplaar met Azure Monitor logboeken](container-instances-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** Profiteer van oplossingen voor het scannen van containerafbeeldingen in een privéregister en het identificeren van mogelijke beveiligingsproblemen. Het is belangrijk om inzicht te krijgen in de diepte van de detectie van bedreigingen die de verschillende oplossingen bieden. Volg de aanbevelingen van Azure Security Center over het uitvoeren van evaluaties van beveiligingsleeds op uw container-afbeeldingen. Implementeer optioneel oplossingen van derden van Azure Marketplace om beveiligingsprobleembeoordelingen voor afbeeldingen uit te voeren.

- [Aanbevelingen voor containerbewaking en -scanbeveiliging voor Azure Container Instances](container-instances-image-security.md)

- [Azure Container Registry integratie met Security Center](/azure/security-center/azure-container-registry-integration)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Een geautomatiseerde beheeroplossing voor besturingssysteempatchs implementeren

**Richtlijnen:** Microsoft voert patchbeheer uit op de onderliggende systemen die ondersteuning bieden voor het uitvoeren van containers.

Automatiseer updates van de containerinstallatie installatie afbeelding wanneer er updates voor basisinstallatie afbeeldingen van het besturingssysteem en andere patches worden gedetecteerd.

- [Updates van basisafbeeldingen voor Azure Container Registry taken](../container-registry/container-registry-tasks-base-images.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een geautomatiseerde oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** U kunt een oplossing van derden gebruiken om toepassingsafbeeldingen te patchen. Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, kunt u ook Azure Container Registry-taken uitvoeren om updates voor toepassingsafbeeldingen in een containerregister te automatiseren op basis van beveiligingspatches of andere updates in basisafbeeldingen.

- [Over updates van basisafbeeldingen voor ACR-taken](../container-registry/container-registry-tasks-base-images.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, integreert u Azure Container Registry (ACR) met Azure Security Center om het periodiek scannen van containerafbeeldingen op beveiligingsproblemen mogelijk te maken. Implementeer optioneel oplossingen van derden van Azure Marketplace om periodieke scans voor beveiligingsprobleem van afbeeldingen uit te voeren.

- [Azure Container Registry integratie met Security Center](../security-center/defender-for-container-registries-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry met Azure Container Instances, integreert u Azure Container Registry (ACR) met Azure Security Center om het periodiek scannen van containerafbeeldingen op beveiligingsproblemen en het classificeren van risico's mogelijk te maken. Implementeer optioneel oplossingen van derden van Azure Marketplace om periodieke scans voor beveiligingsleed van afbeeldingen en risicoclassificatie uit te voeren.

- [Azure Container Registry integratie met Security Center](../security-center/defender-for-container-registries-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen/ontdekken van alle resources (zoals compute, opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement(en). Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, onderhoudt ACR metagegevens, inclusief tags en manifesten voor afbeeldingen in een register. Volg de aanbevolen procedures voor het taggen van artefacten.

- [Over registers, opslagplaatsen en afbeeldingen](../container-registry/container-registry-concepts.md)

- [Aanbevelingen voor het taggen en versieren van containerafbeeldingen](../container-registry/container-registry-image-tag-version.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, onderhoudt ACR metagegevens, inclusief tags en manifesten voor afbeeldingen in een register. Volg de aanbevolen procedures voor het taggen van artefacten.

- [Over registers, opslagplaatsen en afbeeldingen](../container-registry/container-registry-concepts.md)

- [Aanbevelingen voor het taggen en versieren van containerafbeeldingen](../container-registry/container-registry-image-tag-version.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** u moet een inventaris maken van goedgekeurde Azure-resources op de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnement(en) kan worden gemaakt.

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen hun abonnement(en). Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, analyseert en controleert u Azure Container Registry-logboeken op afwijkende gedragingen en controleert u regelmatig de resultaten. Gebruik Azure Monitor Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens.

- [Azure Container Registry voor diagnostische evaluatie en controle](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Inzicht in Log Analytics-werkruimte](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van workloads en resources. U kunt uw eigen oplossing implementeren voor het verwijderen van niet-geautoriseerde Azure-resources. 

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Niet van toepassing. Benchmark is ontworpen voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Niet van toepassing. Benchmark is ontworpen voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Besturingssysteemspecifieke configuraties of resources van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-rekenresources te beperken.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren in rekenbronnen beperken

**Richtlijnen:** Besturingssysteemspecifieke configuraties of resources van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-rekenresources te beperken.

- [Bijvoorbeeld hoe u de uitvoering van PowerShell-scripts in Windows-omgevingen kunt beheren](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Software die vereist is voor zakelijke activiteiten, maar die een hoger risico voor de organisatie kan vormen, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende worden beveiligd met een Azure Firewall of netwerkbeveiligingsgroep.

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy of Azure Security Center om beveiligingsconfiguraties voor alle Azure-resources te onderhouden.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige besturingssysteemconfiguraties maken

**Richtlijnen:** Gebruik Azure Security Center aanbeveling 'Beveiligingsproblemen in beveiligingsconfiguraties op uw Virtual Machines herstellen' om beveiligingsconfiguraties op alle rekenbronnen te onderhouden.

- [Aanbevelingen voor Azure Security Center bewaken](../security-center/security-center-recommendations.md)

- [Aanbevelingen Azure Security Center herstellen](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** Gebruik Azure-beleid [weigeren] en [implementeren indien niet aanwezig] effecten om veilige instellingen af te dwingen voor uw Azure-resources.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, controleert u de naleving van Azure-containerregisters met behulp Azure Policy:.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Niet van toepassing; dit besturingselement is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure-repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste besturingssysteeminstallatie afbeeldingen

**Richtlijnen:** Niet van toepassing; Dit besturingselement is alleen van toepassing op rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, controleert u de naleving van Azure-containerregisters met behulp Azure Policy:.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Niet van toepassing. Benchmark is van toepassing op rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** Gebruik Azure Security Center basislijnscans uit te voeren voor uw Azure-resources. 

Pas Azure Policy om beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, controleert u de naleving van Azure-containerregisters met behulp Azure Policy:.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** Gebruik Azure Security Center basislijnscans uit te voeren voor besturingssysteem- en Docker-instellingen voor containers. 

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Verifiëren bij Key Vault](container-instances-managed-identity.md)

- [Een toegangsbeleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

- [Beheerde identiteiten gebruiken met Azure Container Instances](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Gegevens versleutelen met Container Instances](container-instances-encrypt-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Azure Key Vault zonder referenties in uw code.

Als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, controleert u de naleving van Azure-containerregisters met behulp Azure Policy:.

- [Naleving van Azure-containerregisters controleren met Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Beheerde identiteiten gebruiken met Azure Container Instances](container-instances-managed-identity.md)

- [Een beheerde Azure-identiteit gebruiken om te verifiëren bij een Azure-containerregister](../container-registry/container-registry-authentication-managed-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Centraal beheerde antimalwaresoftware gebruiken

**Richtlijnen:** gebruik Microsoft Antimalware voor Azure Cloud Services en Virtual Machines uw resources continu te bewaken en te beschermen. Gebruik voor Linux de antimalwareoplossing van derden.

- [Een Microsoft Antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Microsoft Antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Container Instances), maar wordt niet uitgevoerd op klantgegevens.

Scan vooraf alle bestanden die worden geüpload naar niet-compute Azure-resources, zoals App Service, Data Lake Storage, Blob Storage, enzovoort.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** als u een privéregister in de cloud gebruikt, zoals Azure Container Registry (ACR) met Azure Container Instances, worden de gegevens in uw Microsoft Azure-containerregister altijd automatisch gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Azure Container Registry kopieert uw gegevens zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen.

Geo-replicatie van een containerregister voor het onderhouden van registerreplica's in meerdere Azure-regio's (optioneel).

- [Geo-replicatie in Azure Container Registry](../container-registry/container-registry-geo-replication.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** u kunt eventueel een back-up maken van container-afbeeldingen door het ene register naar het andere te importeren.

Back-up van door de klant beheerde sleutels in Azure Key Vault azure-opdrachtregelprogramma's of SDK's.

- [Containerafbeeldingen importeren in een containerregister](../container-registry/container-registry-import-images.md)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Implementatiegegevens versleutelen met Container Instances](container-instances-encrypt-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Test het herstel van door de klant beheerde back-upsleutels in Azure Key Vault azure-opdrachtregelprogramma's of SDK's.

- [Sleutels voor Azure Key Vault herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** u kunt de Soft-Delete inschakelen Azure Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering.

- [Het inschakelen Soft-Delete in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST's Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Azure Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in het zoeken of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een kwaadwillende intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast kunt u abonnementen markeren met behulp van tags en een naamgevingssysteem maken om Azure-resources te identificeren en categoriseren, met name de resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de kritiekheid van de Azure-resources en -omgeving waar het incident zich heeft voorgedaan. 

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefen regelmatig om de mogelijkheden voor het reageren op incidenten van uw systemen te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor programma's voor testen, training en oefeningen voor IT-plannen en -mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center en aanbevelingen met behulp van de functie Continue export. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

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
