---
title: Azure-beveiligings basislijn voor Service Fabric
description: De Service Fabric Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285435"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Azure-beveiligings basislijn voor Service Fabric

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark versie 1.0](../security/benchmarks/overview-v1.md) service Fabric. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op service Fabric.

> [!NOTE]
> **Besturings elementen** die niet van toepassing zijn op service Fabric of waarvoor de verantwoordelijkheid van micro soft is, zijn uitgesloten. Als u wilt zien hoe Service Fabric volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het **[volledige service Fabric beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: Zorg ervoor dat alle implementaties van Virtual Network subnet een netwerk beveiligings groep hebben die wordt toegepast op de vertrouwde poorten en bronnen van uw toepassing. 

- [Azure Firewall implementeren met behulp van een sjabloon](../firewall/deploy-template.md)

- [Perimeter netwerken maken met behulp van Azure-netwerk beveiligings groepen (Nsg's)](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Uw Azure Service Fabric-cluster integreren met een bestaand virtueel netwerk](service-fabric-patterns-networking.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Security Center en herstel aanbevelingen voor netwerk beveiliging voor het virtuele netwerk, het subnet en de netwerk beveiligings groep die wordt gebruikt voor het beveiligen van uw Azure service Fabric-cluster. Schakel logboeken stroom voor netwerk beveiligings groepen in en verzend logboeken naar een Azure Storage account naar Traffic audit. U kunt ook stroom logboeken voor netwerk beveiligings groepen naar een Azure Log Analytics-werk ruimte verzenden en Azure Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Azure Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: bieden een front-end-gateway voor het bieden van één punt aan binnenkomend verkeer voor gebruikers, apparaten of andere toepassingen. Azure API Management integreert rechtstreeks met Service Fabric, zodat u de toegang tot back-end-services kunt beveiligen, DOS-aanvallen kunt voor komen door beperking te gebruiken en de API-sleutels, JWT-tokens, certificaten en andere referenties te controleren.

Overweeg het implementeren van Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in.

- [Overzicht van Service Fabric met Azure API Management](service-fabric-api-management-overview.md)

- [API Management in een intern VNET integreren met Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: voor beveiliging van DDoS-aanvallen schakelt u Azure DDoS Standard-beveiliging in op het virtuele netwerk waar uw Azure service Fabric-cluster wordt geïmplementeerd. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel logboeken stroom voor netwerk beveiligings groepen in voor de netwerk beveiligings groep die is gekoppeld aan het subnet dat wordt gebruikt om uw service Fabric-cluster te beveiligen. Registreer de NSG-stroom Logboeken in een Azure Storage-account om stroom records te genereren. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Azure Network Watcher pakket vastleggen in.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

- [Traffic Analytics gebruiken om NSG-stroom logboeken te visualiseren](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Selecteer een aanbieding op de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen en functies voor Payload-inspectie.  Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. 

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md) 

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Azure-toepassing Gateway implementeren voor webtoepassingen waarvoor https/SSL is ingeschakeld voor vertrouwde certificaten. 

- [Application Gateway implementeren](../application-gateway/quick-create-portal.md)

- [Application Gateway configureren voor het gebruik van HTTPS](../application-gateway/create-ssl-portal.md)

- [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](../application-gateway/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor virtuele netwerken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen (NSG) die zijn gekoppeld aan het subnet waarin uw Azure service Fabric-cluster is geïmplementeerd. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service tags van virtueel netwerk](../virtual-network/service-tags-overview.md)

- [Aanbevolen procedures voor netwerken Service Fabric](service-fabric-best-practices-networking.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die betrekking hebben op uw Azure service Fabric-cluster. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. ServiceFabric ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Service Fabric-cluster te controleren of af te dwingen.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door het verpakken van sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk-definitie. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw service Fabric cluster. Voor afzonderlijke regels voor netwerk beveiligings groepen gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte, de duur, enzovoort voor regels die verkeer naar/van een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of de Azure-opdracht regel interface (CLI) gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure service Fabric-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: u kunt uw Azure service Fabric-cluster onboarden naar Azure monitor voor het verzamelen van beveiligings gegevens die door het cluster zijn gegenereerd. Bekijk voor beelden van diagnostische problemen en oplossingen met Service Fabric.

- [Integratie van Azure Monitor logboeken configureren met Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor logboeken instellen voor het bewaken van containers in azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Algemene Service Fabric scenario's diagnosticeren](service-fabric-diagnostics-common-scenarios.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Azure monitor in voor het service Fabric cluster, stuur het naar een log Analytics-werk ruimte. Hiermee worden relevante cluster informatie en metrische gegevens van het besturings systeem geregistreerd voor alle Azure Service Fabric cluster knooppunten.

- [Integratie van Azure Monitor logboeken configureren met Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor logboeken instellen voor het bewaken van containers in azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [De Log Analytics-agent implementeren op uw knoop punten](service-fabric-diagnostics-oms-agent.md)

- [Zoek opdrachten in logboek Log Analytics](/azure/azure-monitor/log-query/log-query-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Hulp**: de Azure service Fabric-cluster onboarden naar Azure monitor. Zorg ervoor dat in de Log Analytics-werk ruimte de Bewaar periode voor logboek registratie is ingesteld op basis van de nalevings voorschriften van uw organisatie.

- [Integratie van Azure Monitor logboeken configureren met Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor logboeken instellen voor het bewaken van containers in azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [De Log Analytics-agent implementeren op uw knoop punten](service-fabric-diagnostics-oms-agent.md) 

- [De Bewaar periode van Log Analytics Workspace configureren](/azure/azure-monitor/platform/manage-cost-storage)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: het service Fabric cluster onboarden naar Azure monitor. Zorg ervoor dat in de Log Analytics-werk ruimte de Bewaar periode voor logboek registratie is ingesteld op basis van de nalevings voorschriften van uw organisatie.

- [Integratie van Azure Monitor logboeken configureren met Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor logboeken instellen voor het bewaken van containers in azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [De Log Analytics-agent implementeren op uw knoop punten](service-fabric-diagnostics-oms-agent.md)

- [De Bewaar periode van Log Analytics Workspace configureren](/azure/azure-monitor/platform/manage-cost-storage)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: gebruik Azure log Analytics Workspace-Query's om Azure service Fabric-logboeken te doorzoeken.

- [Zoek opdrachten in logboek Log Analytics](/azure/azure-monitor/log-query/log-query-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure log Analytics-werk ruimte voor het bewaken en waarschuwen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen die betrekking hebben op uw Azure service Fabric-cluster.

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: standaard is Windows Defender geïnstalleerd op windows server 2016. Raadpleeg de Antimaleware-documentatie voor configuratie regels als u geen gebruik maakt van Windows Defender. Windows Defender wordt niet ondersteund in Linux.

- [Zie Windows Defender anti virus op Windows Server 2016 voor meer informatie.](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Hulp**: Implementeer een oplossing van derden voor DNS-logboek registratie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: hand matig console logboek registratie configureren per knoop punt.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud de record van het lokale beheerders account dat is gemaakt tijdens het inrichten van het cluster van Azure service Fabric cluster, evenals andere accounts die u maakt. Als Azure Active Directory-integratie (Azure AD) wordt gebruikt, heeft Azure AD bovendien ingebouwde rollen die expliciet moeten worden toegewezen en die daarom kunnen worden opgevraagd. Gebruik de Azure AD Power shell-module om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: bij het inrichten van een cluster moet u voor Azure nieuwe wacht woorden maken voor de webportal. Er zijn geen standaard wachtwoorden om te wijzigen, maar u kunt ook verschillende wacht woorden opgeven voor toegang tot de webportal.

- [Maken in Azure-portal](service-fabric-cluster-creation-via-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: Integreer verificatie voor Service Fabric met Azure Active Directory (Azure AD). Beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken.

Daarnaast kunt u de aanbevelingen van Security Center identiteits-en toegangs beheer gebruiken.

- [Azure AD-client verificatie instellen](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory (Azure AD) SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Security Center aanbevelingen voor identiteits-en toegangs beheer. 

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access Workstation) met meervoudige verificatie om u aan te melden en uw service Fabric-clusters en gerelateerde resources te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's. 

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem om de toegang tot beheer eindpunten van service Fabric clusters te beveiligen. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD voor Service Fabric-client authenticatie instellen](service-fabric-cluster-creation-setup-aad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory-verificatie (Azure AD) met uw service Fabric cluster. Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (Azure AD)-aanmeld-en audit Logboeken om te controleren op pogingen om toegang te krijgen tot gedeactiveerde accounts. deze logboeken kunnen worden geïntegreerd in een SIEM/bewakings programma van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Azure Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in azure Log Analytics-werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) risico-en identiteits beveiligings functies voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Gebruik labels voor bronnen die betrekking hebben op uw service Fabric cluster implementaties om te helpen bij het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door Virtual Network of subnet, op de juiste wijze worden gelabeld en beveiligd door een netwerk beveiligings groep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn. Voor Virtual Machines het opslaan of verwerken van gevoelige gegevens, implementeert u beleid en procedures om ze uit te scha kelen wanneer ze niet worden gebruikt. 

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: Implementeer een geautomatiseerd hulp programma op netwerk verbindingen die worden bewaakt voor niet-geautoriseerde overdracht van gevoelige informatie en blokkeert deze overdrachten tijdens het melden van informatie over de beveiliging van uw mede werkers. 

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen. 

Voor Service Fabric wederzijdse verificatie van client naar knoop punt gebruikt u een X. 509-certificaat voor de server identiteit en TLS-versleuteling van http-communicatie. Een wille keurig aantal extra certificaten kan worden geïnstalleerd op een cluster voor toepassings beveiliging, inclusief versleuteling en ontsleuteling van toepassings configuratie waarden en gegevens tussen knoop punten tijdens de replicatie.
Volg Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing. 

- [Meer informatie over versleuteling in transit met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Beveiligings scenario's voor Service Fabric cluster](service-fabric-cluster-security.md)

- [Service Fabric probleemoplossings gids voor TLS-configuratie](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: voor service Fabric clusters die gevoelige informatie opslaan of verwerken, markeert u het cluster en gerelateerde resources als gevoelig voor het gebruik van labels. De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Gebruik versleuteling in rust voor alle Azure-resources. Micro soft raadt u aan Azure te laten beheren van uw versleutelings sleutels, maar in sommige gevallen kunt u ook uw eigen sleutels beheren. 

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)  

- [Door de klant beheerde versleutelings sleutels configureren](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Schijf versleuteling inschakelen voor Azure Service Fabric cluster knooppunten in Windows](service-fabric-enable-azure-disk-encryption-windows.md)

- [Schijf versleuteling inschakelen voor Azure Service Fabric cluster knooppunten in Linux](service-fabric-enable-azure-disk-encryption-linux.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden doorgevoerd in essentiële Azure-resources. 

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Voer regel matig de service Fabric fout analyse service en chaos-services uit om fouten in het cluster te simuleren om de robuustheid en betrouw baarheid van uw services te beoordelen.

Volg de aanbevelingen van Security Center over het uitvoeren van beveiligings evaluaties op uw virtuele machines van Azure en container installatie kopieën. 

Gebruik een oplossing van derden voor het uitvoeren van evaluatie van beveiligings problemen op netwerk apparaten en webtoepassingen. Gebruik bij het uitvoeren van externe scans geen enkele, permanente, beheerders account. Overweeg de implementatie van JIT-inrichtings methodologie voor het account van de scan. Referenties voor het Scan account moeten worden beveiligd, gecontroleerd en alleen worden gebruikt voor het scannen van beveiligings problemen. 

- [Inleiding tot Service Fabric-fout analyse service](service-fabric-testability-overview.md)

- [Beheerde chaos in Service Fabric clusters induceren](service-fabric-controlled-chaos.md)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp** bij het inschakelen van automatische installatie kopieën van besturings systemen op de schaal sets voor virtuele machines van uw service Fabric cluster. 

U kunt ook de hand matige trigger voor upgrades van installatie kopieën van de schaalset gebruiken om de patches voor het besturings systeem eerst te testen voordat u naar de productie gaat. Houd er rekening mee dat de optie hand matige trigger geen ingebouwde terugdraai actie biedt. BESTURINGSSYSTEEM patches bewaken met behulp van Updatebeheer van Azure Automation. 

- [Patch beheer voor Service Fabric cluster knooppunten](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatische installatie kopieën van besturings systemen met virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Vm's up-to-date zetten met het nieuwste model voor schaal sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Overzicht van Azure Automation Updatebeheer](/azure/automation/update-management/update-mgmt-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Hulp** bij het automatisch bijwerken van installatie kopieën van besturings systemen op de schaal sets voor virtuele machines van uw Azure service Fabric-cluster. Patch Orchestration Application (POA) is een alternatieve oplossing die is bedoeld voor Service Fabric clusters die buiten Azure worden gehost. POA kan worden gebruikt met Azure-clusters, met een extra hosting overhead.

- [Patch beheer voor Service Fabric cluster knooppunten](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatische installatie kopieën van besturings systemen met virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Het patch schema voor het besturings systeem voor Service Fabric clusters configureren](service-fabric-patch-orchestration-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: scan resultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligings problemen zijn opgelost. Wanneer u aanbevelingen voor beveiligings beheer gebruikt die worden voorgesteld door Security Center, kunt u in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld een gemeen schappelijk score systeem voor beveiligings problemen) of de standaard risico classificaties van het hulp programma van derden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

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

**Hulp**: Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

Niet toegestane resourcetypen

Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources in uw abonnementen. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: Implementeer een oplossing van derden om cluster knooppunten te bewaken voor niet-goedgekeurde software toepassingen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief service Fabric clusters, binnen uw abonnementen. Verwijder alle niet-goedgekeurde Azure-resources die u ontdekt. Implementeer voor Service Fabric cluster knooppunten een oplossing van derden om niet-goedgekeurde software te verwijderen of te waarschuwen.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: voor service Fabric cluster knooppunten implementeert u een oplossing van derden om te voor komen dat onbevoegde software wordt uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: voor Azure service Fabric cluster knooppunten implementeert u een oplossing van derden om te voor komen dat niet-geautoriseerde bestands typen worden uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management. 

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: gebruik specifieke configuraties van het besturings systeem of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.

- [Bijvoorbeeld hoe u de uitvoering van Power shell-scripts kunt beheren in Windows-omgevingen](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: software die vereist is voor bedrijfs activiteiten, maar die een groter risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure firewall of netwerk beveiligings groep. 

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md) 

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ServiceFabric ' om aangepaste beleids regels te maken om de netwerk configuratie van uw service Fabric cluster te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Hulp**: Service Fabric installatie kopieën van besturings systemen worden beheerd en beheerd door micro soft. De klant die verantwoordelijk is voor het implementeren van beveiligde configuraties voor het besturings systeem van uw cluster knooppunten.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure service Fabric-clusters en gerelateerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Hulp**: Service Fabric installatie kopieën van het besturings systeem cluster worden beheerd en onderhouden door micro soft. De klant is verantwoordelijk voor het implementeren van de status configuratie op besturingssysteem niveau.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt, moet u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om ervoor te zorgen dat alleen gemachtigde gebruikers toegang kunnen krijgen tot de installatie kopieën. Voor container installatie kopieën slaat u ze op in Azure Container Registry en maakt u gebruik van Azure RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën. 

- [Meer informatie over Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Meer informatie over Azure RBAC voor Container Registry](../container-registry/container-registry-roles.md) 

- [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ServiceFabric ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ServiceFabric ' om aangepaste beleids regels te maken om de configuratie van uw service Fabric cluster te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: gebruik Security Center voor het uitvoeren van basislijn scans voor OS-en docker-instellingen voor containers. 

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. 

- [Beheerde identiteiten gebruiken voor Azure met Service Fabric](concepts-managed-identity.md)

- [Ondersteuning voor beheerde identiteiten configureren voor een nieuw Service Fabric cluster](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Beheerde identiteit gebruiken met een Service Fabric-toepassing](how-to-managed-identity-service-fabric-app-code.md)

- [KeyVaultReference-ondersteuning voor Service Fabric toepassingen](service-fabric-keyvault-references.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: beheerde identiteiten kunnen worden gebruikt in door Azure geïmplementeerde service Fabric clusters en voor toepassingen die als Azure-resources worden geïmplementeerd. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure Active Directory-verificatie (Azure AD), met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten gebruiken voor Azure met Service Fabric](concepts-managed-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: als u code gebruikt die betrekking heeft op uw Azure service Fabric-implementatie, kunt u referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Gebruik Azure Key Vault om Service Fabric cluster certificaten automatisch te draaien.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Certificaat beheer in Service Fabric clusters](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: Windows Defender anti virus is standaard geïnstalleerd op windows server 2016. De gebruikersinterface wordt standaard geïnstalleerd op een aantal SKU's, maar is niet vereist.

Raadpleeg de documentatie van uw anti-malware voor configuratie regels als u geen gebruik maakt van Windows Defender. Windows Defender wordt niet ondersteund in Linux.

- [Informatie over Windows Defender anti virus op Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: de service voor back-up en herstel in service Fabric maakt eenvoudige en automatische back-ups mogelijk van gegevens die zijn opgeslagen in stateful Services. Het maken van een back-up van toepassings gegevens op periodieke basis is essentieel voor de beveiliging tegen gegevens verlies en service niet beschikbaar. Service Fabric biedt een optionele service voor back-up en herstel, waarmee u periodieke back-ups kunt configureren van stateful Reliable Services (inclusief actor Services) zonder dat u extra code hoeft te schrijven. Het vereenvoudigt ook het herstellen van eerder gemaakte back-ups.

- [Periodieke back-ups maken en herstellen in een Azure Service Fabric-cluster](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp** bij het inschakelen van de service voor het terugzetten van back-ups in uw service Fabric-cluster en het maken van een back-upbeleid om stateful Services periodiek en op aanvraag uit te voeren. Maak een back-up van door de klant beheerde sleutels binnen Azure Key Vault.

- [Periodieke back-ups maken en herstellen in een Azure Service Fabric-cluster](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Informatie over periodieke back-upconfiguratie in azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Zorg ervoor dat u de herstel service van de back-up kunt herstellen door regel matig informatie over de back-upconfiguratie en beschik bare back-ups te controleren. Het herstellen van een back-up van door de klant beheerde sleutels testen.

- [Informatie over periodieke back-upconfiguratie in azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Back-up herstellen in azure Service Fabric](service-fabric-backup-restore-service-trigger-restore.md)

- [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: back-ups van service Fabric service voor het terugzetten van back-ups maken gebruik van een Azure Storage-account in uw abonnement. Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels voor versleuteling van opslag gegevens leveren.

Als u door de klant beheerde sleutels gebruikt, zorgt u ervoor dat Soft-Delete in Key Vault is ingeschakeld voor het beveiligen van sleutels tegen onbedoelde of schadelijke verwijdering.

- [Versleuteling-at-rest in Azure Storage](../storage/common/storage-service-encryption.md)

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident. 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de metriek die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Markeer bovendien abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden. 

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan. 

- [NIST-hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost. 

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Security Center Data Connector gebruiken om de waarschuwingen naar Sentinel te verzenden.

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
