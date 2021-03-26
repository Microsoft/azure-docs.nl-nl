---
title: Azure-beveiligings basislijn voor HDInsight
description: De basis lijn voor HDInsight-beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565007"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure-beveiligings basislijn voor HDInsight

In deze beveiligings basislijn wordt richt lijn toegepast van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) naar HDInsight. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op HDInsight. **Besturings elementen** die niet van toepassing zijn op HDInsight, zijn uitgesloten.

 
Zie voor meer informatie over hoe HDInsight volledig is toegewezen aan de Security Bench Mark van Azure de [volledige hdinsight-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: de beveiliging van een perimeter netwerk in azure HDInsight wordt bereikt via virtuele netwerken. Een Enter prise-beheerder kan een cluster maken in een virtueel netwerk en een netwerk beveiligings groep (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de regels voor binnenkomende netwerk beveiligings groepen kunnen communiceren met het Azure HDInsight-cluster. Deze configuratie biedt een perimeterbeveiliging. Alle clusters die in een virtueel netwerk zijn geïmplementeerd, hebben ook een persoonlijk eind punt dat wordt omgezet in een privé-IP-adres in de Virtual Network voor persoonlijke HTTP-toegang tot de cluster gateways.

Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

- [Azure HDInsight implementeren binnen een Virtual Network en beveiligen met een netwerk beveiligings groep](hdinsight-create-virtual-network.md)

- [Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall](hdinsight-restrict-outbound-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging voor het virtuele netwerk, het subnet en de netwerk beveiligings groep die wordt gebruikt voor het beveiligen van uw Azure HDInsight-cluster. Schakel de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzend logboeken naar een Azure Storage account naar verkeers controle. U kunt ook NSG-stroom logboeken naar een Azure Log Analytics-werk ruimte verzenden en Azure Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Azure Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerkloze configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: voor beveiliging van DDoS-aanvallen schakelt u Azure DDoS Standard-beveiliging in op het virtuele netwerk waar uw Azure HDInsight is geïmplementeerd. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel de stroom logboeken voor netwerk beveiligings groepen (NSG) in voor de NSG die zijn gekoppeld aan het subnet dat wordt gebruikt om uw Azure HDInsight-cluster te beveiligen. Registreer de NSG-stroom Logboeken in een Azure Storage-account om stroom records te genereren. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Azure Network Watcher pakket vastleggen in.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: er kan worden voldaan aan de Azure security control id 1,1; Implementeer Azure HDInsight-cluster in een virtueel netwerk en Beveilig het met een netwerk beveiligings groep (NSG).

Er zijn verschillende afhankelijkheden voor Azure HDInsight waarvoor binnenkomend verkeer is vereist. Het inkomende beheer verkeer kan niet via een firewall apparaat worden verzonden. De bron adressen voor het vereiste beheer verkeer worden bekend en gepubliceerd. Maak met deze informatie regels voor netwerk beveiligings groepen om alleen verkeer vanaf vertrouwde locaties toe te staan, zodat inkomend verkeer naar de clusters kan worden beveiligd.

Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

- [HDInsight implementeren in een Virtual Network en beveiligen met een netwerk beveiligings groep](hdinsight-create-virtual-network.md)

- [Informatie over HDInsight-afhankelijkheden en firewall gebruik](hdinsight-restrict-outbound-traffic.md)

- [IP-adressen beheren met HDInsight](hdinsight-management-ip-addresses.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor virtuele netwerken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen (NSG) die zijn gekoppeld aan het subnet waarin uw Azure HDInsight-cluster is geïmplementeerd. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service tags voor Azure HDInsight begrijpen en gebruiken](../virtual-network/network-security-groups-overview.md#service-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk bronnen die betrekking hebben op uw Azure HDInsight-cluster. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. HDInsight ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure HDInsight-cluster te controleren of af te dwingen.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door het verpakken van sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk-definitie. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (nsg's) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw Azure HDInsight-cluster. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of de Azure-opdracht regel interface (CLI) gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure HDInsight-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: u kunt uw Azure HDInsight-cluster onboarden om Azure monitor te genereren voor het verzamelen van beveiligings gegevens die door het cluster zijn gegenereerd. Gebruik aangepaste query's om bedreigingen in de omgeving te detecteren en erop te reageren. 

- [Een Azure HDInsight-cluster onboarden naar Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Aangepaste query's maken voor een Azure HDInsight-cluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Azure monitor in voor het HDInsight-cluster, stuur het naar een log Analytics-werk ruimte. Hiermee worden relevante cluster informatie en metrische gegevens van het besturings systeem geregistreerd voor alle Azure HDInsight-cluster knooppunten.

- [Logboek registratie inschakelen voor HDInsight-cluster](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Een query uitvoeren op HDInsight-logboeken](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: onboarding van Azure HDInsight-cluster naar Azure monitor. Zorg ervoor dat in de Log Analytics-werk ruimte de Bewaar periode voor logboek registratie is ingesteld op basis van de nalevings voorschriften van uw organisatie.

- [Een Azure HDInsight-cluster onboarden naar Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/logs/manage-cost-storage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: onboarding van Azure HDInsight-cluster naar Azure monitor. Zorg ervoor dat de gebruikte Azure Log Analytics-werk ruimte de Bewaar periode van het logboek heeft ingesteld volgens de nalevings voorschriften van uw organisatie.

- [Een Azure HDInsight-cluster onboarden naar Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/logs/manage-cost-storage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Azure log Analytics werkruimte query's gebruiken om een query uit te zoeken in azure HDInsight-logboeken:

- [Aangepaste Query's maken voor Azure HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Azure log Analytics-werk ruimte gebruiken voor bewaking en waarschuwingen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen die betrekking hebben op uw Azure HDInsight-cluster.

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën, maar u moet de software echter wel beheren en hand matig aggregatie of bewakings logboeken clamscan genereren.

- [Informatie over clamscan](./hdinsight-faq.md#security-and-certificates)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: oplossingen van derden voor DNS-logboek registratie implementeren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: hand matig console logboek registratie configureren per knoop punt.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud de record van het lokale beheerders account dat is gemaakt tijdens het inrichten van het cluster van Azure HDInsight-cluster en andere accounts die u maakt. Als Azure Active Directory-integratie (Azure AD) wordt gebruikt, heeft Azure AD bovendien ingebouwde rollen die expliciet moeten worden toegewezen en die daarom kunnen worden opgevraagd. Gebruik de Azure AD Power shell-module om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: bij het inrichten van een cluster moet u voor Azure nieuwe wacht woorden maken voor de webportal en SSH-toegang (Secure Shell). Er zijn geen standaard wachtwoorden om te wijzigen, maar u kunt ook verschillende wacht woorden voor SSH-en web portal-toegang opgeven.

- [Wacht woorden instellen bij het inrichten van een Azure HDInsight-cluster](hdinsight-hadoop-linux-use-ssh-unix.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: Integreer verificatie voor Azure HDInsight-cluster met Azure Active Directory (Azure AD). Beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

- [Azure HDInsight-verificatie integreren met Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik Azure HDInsight id Broker om u aan te melden bij Enterprise Security Package-clusters (ESP) met behulp van multi-factor Authentication, zonder wacht woorden op te geven. Als u zich al hebt aangemeld bij andere Azure-Services, zoals de Azure Portal, kunt u zich aanmelden bij uw Azure HDInsight-cluster met een SSO-ervaring (eenmalige aanmelding).

- [Azure HDInsight ID Broker inschakelen](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters en big data-taken uit te voeren. Wanneer verificatie met meervoudige verificatie is ingeschakeld, worden gebruikers gevraagd een tweede verificatie factor op te geven.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met multi-factor Authentication om u aan te melden en uw Azure HDInsight-clusters en gerelateerde resources te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren. U kunt de beveiligings rapporten van Azure Active Directory (Azure AD) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de Azure AD-omgeving plaatsvinden. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren. Gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Azure HDInsight-clusters met Enterprise Security Package (ESP) die zijn geconfigureerd, kunnen worden verbonden met een domein zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Enterprise Security Package configureren met Azure AD Domain Services in azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory-verificatie (Azure AD) met uw Azure HDInsight-cluster. Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (Azure AD)-aanmeld-en audit Logboeken om te controleren op pogingen om toegang te krijgen tot gedeactiveerde accounts. deze logboeken kunnen worden geïntegreerd in een SIEM/bewakings programma van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Azure Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in azure Log Analytics-werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: Azure HDInsight-clusters met Enterprise Security Package (ESP) die zijn geconfigureerd, kunnen worden verbonden met een domein zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters. Gebruik Azure Active Directory (Azure AD) risico detecties en functie voor identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Daarnaast kunt u gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet beschikbaar; Klanten-lockbox nog niet ondersteund voor Azure HDInsight.

- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: Gebruik labels op resources die betrekking hebben op uw Azure HDInsight-implementaties om te helpen bij het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure HDInsight-clusters en alle gekoppelde opslag accounts moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of Azure Firewall. Cluster gegevens moeten zich bevinden in een beveiligd Azure Storage account of Azure Data Lake Storage (gen1 of Gen2).

- [Opslag opties kiezen voor uw Azure HDInsight-cluster](hdinsight-hadoop-compare-storage-options.md)

- [Azure Data Lake Storage beveiligen](../data-lake-store/data-lake-store-security-overview.md)

- [Azure Storage accounts beveiligen](../storage/blobs/security-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor Azure HDInsight-clusters waarin gevoelige gegevens worden opgeslagen of verwerkt, markeert u het cluster en gerelateerde resources als gevoelig voor het gebruik van tags. Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall](hdinsight-restrict-outbound-traffic.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure HDInsight-cluster of gegevens archieven van het cluster (Azure Storage accounts of Azure Data Lake Storage Gen1/Gen2), kunnen onderhandelen over TLS 1,2 of hoger. Microsoft Azure resources onderhandelen standaard TLS 1,2. 

- [Meer informatie over Azure Data Lake Storage versleuteling in transit](../data-lake-store/data-lake-store-security-overview.md)

- [Meer informatie over Azure Storage account versleuteling in transit](../storage/blobs/security-recommendations.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Hulp**: met de Enterprise Security Package van Azure HDINSIGHT (ESP) kunt u Apache zwerver gebruiken voor het maken en beheren van beleids regels voor nauw keurig toegangs beheer en gegevens opslag voor uw gegevens die zijn opgeslagen in bestanden, mappen, data bases, tabellen en rijen/kolommen. De Hadoop-beheerder kan op rollen gebaseerd toegangs beheer (RBAC) configureren om Apache Hive, HBase, Kafka en Spark te beveiligen met behulp van deze invoeg toepassingen in Apache zwerver.

Het configureren van het RBAC-beleid met Apache zwerver biedt u de mogelijkheid om machtigingen te koppelen aan een rol in de organisatie. Deze laag van abstractie maakt het gemakkelijker om ervoor te zorgen dat personen alleen de machtigingen hebben die nodig zijn om hun werk verantwoordelijkheden uit te voeren.

- [Enterprise Security Package configuraties met Azure Active Directory (Azure AD) Domain Services in HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Overzicht van bedrijfsbeveiliging in Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: voor Azure HDInsight-clusters waarin gevoelige gegevens worden opgeslagen of verwerkt, markeert u het cluster en gerelateerde resources als gevoelig voor het gebruik van tags. De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: als u Azure SQL database gebruikt om Apache Hive-en Apache Oozie-meta gegevens op te slaan, zorgt u ervoor dat SQL-gegevens altijd versleuteld blijven. Voor Azure Storage accounts en Data Lake Storage (gen1 of Gen2) wordt aanbevolen micro soft toe te staan uw versleutelings sleutels te beheren, maar u hebt de mogelijkheid om uw eigen sleutels te beheren.

- [Versleutelings sleutels voor Azure Storage accounts beheren](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Azure Data Lake Storage maken met door de klant beheerde versleutelings sleutels](../data-lake-store/data-lake-store-get-started-portal.md)

- [Informatie over versleuteling voor Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Transparent Data Encryption voor SQL Database configureren met door de klant beheerde sleutels](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Richt lijnen**: Diagnostische instellingen configureren voor Azure Storage accounts die zijn gekoppeld aan Azure HDInsight-clusters om alle ruwe bewerkingen te controleren en te registreren voor cluster gegevens. Schakel controle in voor opslag accounts of Data Lake archieven die zijn gekoppeld aan het Azure HDInsight-cluster.

- [Aanvullende logboek registratie/controle voor een Azure Storage account inschakelen](../storage/common/manage-storage-analytics-logs.md)

- [Aanvullende logboek registratie/controle voor Azure Data Lake Storage inschakelen](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden.

Als u een abonnement op Rapid7, Qualys of een ander platform voor beveiligings problemen hebt, kunt u ook script acties gebruiken om agents voor beveiligings evaluaties te installeren op uw Azure HDInsight-cluster knooppunten en de knoop punten te beheren via de respectieve Portal.

- [Rapid7 agent hand matig installeren](https://insightvm.help.rapid7.com/docs/install)

- [Qualys agent hand matig installeren](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Script acties gebruiken](hdinsight-hadoop-customize-cluster-linux.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: automatische systeem updates zijn ingeschakeld voor installatie kopieën van cluster knooppunten, maar u moet de cluster knooppunten echter periodiek opnieuw opstarten om ervoor te zorgen dat er updates worden toegepast.

Micro soft voor het onderhouden en bijwerken van basis-Azure HDInsight-knooppunt installatie kopieën.

- [Het patch schema voor het besturings systeem voor HDInsight-clusters configureren](hdinsight-os-patching.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Hulp**: script acties of andere mechanismen gebruiken om uw Azure HDInsight-clusters te patchen. Nieuwe clusters hebben altijd de meest recente beschik bare updates, inclusief de meest recente beveiligings patches.

- [Het patch schema voor het besturings systeem configureren voor Azure HDInsight-clusters op basis van Linux](hdinsight-os-patching.md)

- [Script acties gebruiken](hdinsight-hadoop-customize-cluster-linux.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden die de mogelijkheid biedt om beveiligings controles in de loop van de tijd te vergelijken. Als u een Rapid7-of Qualys-abonnement hebt, kunt u de portal van die leverancier gebruiken voor het weer geven en vergelijken van back-to-back-scans.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld een gemeen schappelijk score systeem voor beveiligings problemen) of de standaard risico classificaties van het hulp programma van derden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources te zoeken en te detecteren (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief Azure HDInsight-clusters, binnen uw abonnementen. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via Azure resource Graph, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

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

**Hulp**: een lijst met goedgekeurde Azure-resources en goedgekeurde software voor uw reken resources definiëren

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph om resources in uw abonnementen op te vragen en te detecteren. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: Implementeer een oplossing van derden om cluster knooppunten te bewaken voor niet-goedgekeurde software toepassingen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure resource Graph gebruiken om alle resources te zoeken en te detecteren (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief Azure HDInsight-clusters, binnen uw abonnementen.  Verwijder alle niet-goedgekeurde Azure-resources die u ontdekt. Voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om niet-goedgekeurde software te verwijderen of te waarschuwen.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om te voor komen dat onbevoegde software wordt uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen

- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om te voor komen dat niet-geautoriseerde bestands typen worden uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om de netwerk configuratie van uw HDInsight-cluster te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: installatie kopieën van het Azure HDInsight-besturings systeem die worden beheerd en beheerd door micro soft. De klant die verantwoordelijk is voor het implementeren van beveiligde configuraties voor het besturings systeem van uw cluster knooppunten.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure HDInsight-clusters en gerelateerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: installatie kopieën van het Azure HDInsight-besturings systeem die worden beheerd en beheerd door micro soft. De klant die verantwoordelijk is voor het implementeren van de status configuratie op besturingssysteem niveau.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

- [Azure opslag plaatsen Git-zelf studie](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Hulp**: Implementeer een oplossing van derden om de gewenste status voor de besturings systemen van het cluster knooppunt te onderhouden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om de configuratie van uw HDInsight-cluster te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: Implementeer een oplossing van derden om de status van de cluster knooppunt besturingssystemen te bewaken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure HDInsight bevat Bring your own Key-ondersteuning (BYOK) voor Apache Kafka. Met deze mogelijkheid kunt u de sleutels die worden gebruikt voor het versleutelen van gegevens in rust krijgen en beheren.

Alle beheerde schijven in azure HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Standaard worden de gegevens op deze schijven versleuteld met door micro soft beheerde sleutels. Als u BYOK inschakelt, geeft u de versleutelings sleutel voor Azure HDInsight op om deze te gebruiken en te beheren met Azure Key Vault.

Key Vault kunnen ook worden gebruikt met Azure HDInsight-implementaties voor het beheren van sleutels voor cluster opslag (Azure Storage accounts en Azure Data Lake Storage)

- [Uw eigen sleutel voor Apache Kafka in azure HDInsight zetten](./disk-encryption.md)

- [Versleutelings sleutels voor Azure Storage accounts beheren](../storage/common/customer-managed-keys-configure-key-vault.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: beheerde identiteiten kunnen worden gebruikt in azure HDInsight om uw clusters toegang te geven tot Azure Active Directory (Azure AD) Domain Services, Access Azure Key Vault of toegang tot bestanden in azure data Lake Storage Gen2.

- [Beheerde identiteiten begrijpen met Azure HDInsight](hdinsight-managed-identities.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: als u code gebruikt die betrekking heeft op uw Azure HDInsight-implementatie, kunt u referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën, maar u moet de software echter wel beheren en hand matig aggregatie of bewakings logboeken clamscan genereren.

- [Meer informatie over clamscan voor Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf bestanden die worden geüpload naar Azure-resources die zijn gerelateerd aan uw Azure HDInsight-cluster implementatie, zoals Data Lake Storage, Blob Storage, enzovoort. Micro soft heeft geen toegang tot klant gegevens in deze instanties.

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën. Clamscan voert automatisch engine-en definitie-updates uit, maar aggregatie en beheer van Logboeken moet hand matig worden uitgevoerd.

- [Meer informatie over clamscan voor Azure Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Richt lijnen**: als u een Azure Storage-account gebruikt voor het gegevens archief van het HDInsight-cluster, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS).  Wanneer u een Azure SQL Database gebruikt voor het Azure HDInsight-cluster gegevens archief, configureert u actieve geo-replicatie.

- [Opslag redundantie configureren voor Azure Storage accounts](../storage/common/storage-redundancy.md)

- [Redundantie voor Azure SQL Database configureren](../azure-sql/database/active-geo-replication-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: als u een Azure Storage-account gebruikt voor de gegevens opslag van het Azure HDInsight-cluster, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS). Als u Azure Key Vault gebruikt voor elk onderdeel van uw Azure HDInsight-implementatie, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

- [Opslag opties kiezen voor uw Azure HDInsight-cluster](hdinsight-hadoop-compare-storage-options.md)

- [Opslag redundantie configureren voor Azure Storage accounts](../storage/common/storage-redundancy.md)

- [Back-ups maken van Key Vault sleutels in azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als Azure Key Vault wordt gebruikt in combi natie met uw Azure HDInsight-implementatie, test u het herstellen van back-ups van door de klant beheerde sleutels.

- [Uw eigen sleutel voor Apache Kafka in azure HDInsight zetten](./disk-encryption.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als Azure Key Vault wordt gebruikt in combi natie met uw Azure HDInsight-implementatie, schakelt u zacht verwijderen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Azure Key Vault voor het inschakelen van zacht verwijderen](../key-vault/general/soft-delete-overview.md)

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

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.

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

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
