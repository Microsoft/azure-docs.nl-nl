---
title: Azure-beveiligingsbasislijn voor HDInsight
description: Azure-beveiligingsbasislijn voor HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5174c3bf2150c4671f1034eba6e661a10c222aec
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807679"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure-beveiligingsbasislijn voor HDInsight

De Azure-beveiligings basislijn voor HDInsight bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Richt lijnen**: de beveiliging van een perimeter netwerk in azure HDInsight wordt bereikt via virtuele netwerken. Een Enter prise-beheerder kan een cluster maken in een virtueel netwerk en een netwerk beveiligings groep (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de regels voor binnenkomende netwerk beveiligings groepen kunnen communiceren met het Azure HDInsight-cluster. Deze configuratie biedt een perimeterbeveiliging. Alle clusters die in een virtueel netwerk zijn geïmplementeerd, hebben ook een persoonlijk eind punt dat wordt omgezet in een privé-IP-adres in de Virtual Network voor persoonlijke HTTP-toegang tot de cluster gateways.

Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

Azure HDInsight implementeren in een Virtual Network en beveiligen met een netwerk beveiligings groep: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging voor het virtuele netwerk, het subnet en de netwerk beveiligings groep die wordt gebruikt voor het beveiligen van uw Azure HDInsight-cluster. Schakel de stroom logboeken voor netwerk beveiligings groepen (NSG) in en verzend logboeken naar een Azure Storage account naar Traffic audit. U kunt ook NSG-stroom logboeken naar een Azure Log Analytics-werk ruimte verzenden en Azure Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Azure Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerkloze configuraties te lokaliseren.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informatie over netwerk beveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor Azure app service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: voor beveiliging van DDoS-aanvallen schakelt u Azure DDoS Standard-beveiliging in op het virtuele netwerk waar uw Azure HDInsight is geïmplementeerd. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: Schakel de flog-logboeken voor netwerk beveiligings groepen (NSG) in voor de NSG die is gekoppeld aan het subnet dat wordt gebruikt om uw Azure HDInsight-cluster te beveiligen. Registreer de NSG-stroom Logboeken in een Azure Storage-account om stroom records te genereren. Als dit nodig is voor het onderzoeken van afwijkende activiteiten, schakelt u Azure Network Watcher pakket vastleggen in.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: er kan worden voldaan aan de Azure security control id 1,1; Implementeer Azure HDInsight-cluster in een virtueel netwerk en Beveilig het met een netwerk beveiligings groep (NSG).

Er zijn verschillende afhankelijkheden voor Azure HDInsight waarvoor binnenkomend verkeer is vereist. Het inkomende beheer verkeer kan niet via een firewall apparaat worden verzonden. De bron adressen voor het vereiste beheer verkeer worden bekend en gepubliceerd. Maak met deze informatie regels voor netwerk beveiligings groepen om alleen verkeer vanaf vertrouwde locaties toe te staan, zodat inkomend verkeer naar de clusters kan worden beveiligd.

Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

HDInsight implementeren in een Virtual Network en beveiligen met een netwerk beveiligings groep: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Informatie over HDInsight-afhankelijkheden en firewall gebruik: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP-adressen van HDInsight-beheer: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor Azure app service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor virtuele netwerken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen (NSG) die zijn gekoppeld aan het subnet waarin uw Azure HDInsight-cluster is geïmplementeerd. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Service tags voor Azure HDInsight begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk bronnen die betrekking hebben op uw Azure HDInsight-cluster. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. HDInsight ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure HDInsight-cluster te controleren of af te dwingen.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk te definiëren. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

Beschik bare Azure Policy aliassen weer geven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (nsg's) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw Azure HDInsight-cluster. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of de Azure-opdracht regel interface (CLI) gebruiken om op basis van hun labels acties te zoeken of uit te voeren op resources.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligings configuratie:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure HDInsight-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt tijd bronnen voor Azure HDInsight-cluster onderdelen, u kunt de tijd synchronisatie voor uw reken implementaties bijwerken.

Tijd synchronisatie voor Azure Compute-resources configureren:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: u kunt uw Azure HDInsight-cluster onboarden om Azure monitor te genereren voor het verzamelen van beveiligings gegevens die door het cluster zijn gegenereerd. Gebruik aangepaste query's om bedreigingen in de omgeving te detecteren en erop te reageren. 

Een Azure HDInsight-cluster onboarden naar Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Aangepaste query's maken voor een Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Azure monitor in voor het HDInsight-cluster, stuur het naar een log Analytics-werk ruimte. Hiermee worden relevante cluster informatie en metrische gegevens van het besturings systeem geregistreerd voor alle Azure HDInsight-cluster knooppunten.

Logboek registratie inschakelen voor HDInsight-cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Informatie over het uitvoeren van een query op HDInsight-logboeken:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: onboarding van Azure HDInsight-cluster naar Azure monitor. Zorg ervoor dat in de Log Analytics-werk ruimte de Bewaar periode voor logboek registratie is ingesteld op basis van de nalevings voorschriften van uw organisatie.

Een Azure HDInsight-cluster onboarden naar Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics retentie periode voor de werk ruimte configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: onboarding van Azure HDInsight-cluster naar Azure monitor. Zorg ervoor dat de gebruikte Azure Log Analytics-werk ruimte de Bewaar periode van het logboek heeft ingesteld volgens de nalevings voorschriften van uw organisatie.

Een Azure HDInsight-cluster onboarden naar Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics retentie periode voor de werk ruimte configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Azure log Analytics werkruimte query's gebruiken om een query uit te zoeken in azure HDInsight-logboeken:

Aangepaste Query's maken voor Azure HDInsight-clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: Azure log Analytics-werk ruimte gebruiken voor bewaking en waarschuwingen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen die betrekking hebben op uw Azure HDInsight-cluster.

Waarschuwingen beheren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Een waarschuwing over logboek gegevens van log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën, maar u moet de software echter wel beheren en hand matig aggregatie of bewakings logboeken clamscan genereren.

Meer informatie over clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: oplossingen van derden voor DNS-logboek registratie implementeren.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: hand matig console logboek registratie configureren per knoop punt.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud de record van het lokale beheerders account dat is gemaakt tijdens het inrichten van het cluster van Azure HDInsight-cluster en andere accounts die u maakt. Als Azure AD-integratie wordt gebruikt, heeft Azure AD bovendien ingebouwde rollen die expliciet moeten worden toegewezen en daarom kunnen query's worden uitgevoerd. Gebruik de Azure AD Power shell-module om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

Een directory-rol verkrijgen in azure AD met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Leden van een directory-rol in azure AD ophalen met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: bij het inrichten van een cluster moet u voor Azure nieuwe wacht woorden maken voor de webportal en SSH-toegang (Secure Shell). Er zijn geen standaard wachtwoorden om te wijzigen, maar u kunt ook verschillende wacht woorden voor SSH-en web portal-toegang opgeven.

Wacht woorden instellen bij het inrichten van een Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: Integreer verificatie voor Azure HDInsight-cluster met Azure Active Directory. Beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

Azure HDInsight-verificatie integreren met Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik Azure HDInsight id Broker om u aan te melden bij Enterprise Security Package (ESP)-clusters met behulp van multi-factor Authentication, zonder dat u wacht woorden hoeft op te geven. Als u zich al hebt aangemeld bij andere Azure-Services, zoals de Azure Portal, kunt u zich aanmelden bij uw Azure HDInsight-cluster met een SSO-ervaring (eenmalige aanmelding).

Azure HDInsight ID Broker inschakelen:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters en big data-taken uit te voeren. Wanneer verificatie met multi-factor Authentication (MFA) is ingeschakeld, worden gebruikers gevraagd een tweede verificatie factor op te geven.

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met multi-factor Authentication (MFA) geconfigureerd om u aan te melden en uw Azure HDInsight-clusters en gerelateerde resources te configureren.

Meer informatie over privileged Access workstations:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren. U kunt de beveiligings rapporten van Azure Active Directory (AAD) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de AAD-omgeving plaatsvinden. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

AAD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: Azure HDInsight-clusters met de geconfigureerde Enterprise Security Package kunnen worden verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren. Gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Aad) als centrale verificatie-en autorisatie systeem. AAD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en tijdens de overdracht. AAD bevat ook zouten, hashes en veilige gebruikers referenties.

Azure HDInsight-clusters met Enterprise Security Package (ESP) die zijn geconfigureerd, kunnen worden verbonden met een domein zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters.

Een AAD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Enterprise Security Package configureren met Azure Active Directory Domain Services in azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory (Aad)-verificatie met uw Azure HDInsight-cluster. AAD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Azure Identity Access revisies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: gebruik Azure Active Directory (Aad)-aanmelding en audit Logboeken om te controleren op pogingen om toegang te krijgen tot gedeactiveerde accounts. deze logboeken kunnen worden geïntegreerd in een SIEM/bewakings programma van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor AAD-gebruikers accounts te maken, de audit logboeken en aanmeldings logboeken te verzenden naar een Azure Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in azure Log Analytics-werk ruimte.

Azure-activiteiten logboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: Azure HDInsight-clusters met Enterprise Security Package (ESP) die zijn geconfigureerd, kunnen worden verbonden met een domein zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters.  Gebruik Azure Active Directory (AAD) risico detecties en de functie identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Daarnaast kunt u gegevens opnemen in azure Sentinel voor verder onderzoek.

Het weer geven van de Risk ante aanmeldingen voor AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet beschikbaar; Klanten-lockbox nog niet ondersteund voor Azure HDInsight.

Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: Gebruik labels op resources die betrekking hebben op uw Azure HDInsight-implementaties om te helpen bij het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure HDInsight-clusters en alle gekoppelde opslag accounts moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of Azure Firewall. Cluster gegevens moeten zich bevinden in een beveiligd Azure Storage account of Azure Data Lake Storage (gen1 of Gen2).

Kies opslag opties voor uw Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Azure Data Lake Storage beveiligen:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Azure Storage accounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor Azure HDInsight-clusters waarin gevoelige gegevens worden opgeslagen of verwerkt, markeert u het cluster en gerelateerde resources als gevoelig voor het gebruik van tags. Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure HDInsight-clusters met behulp van Azure Firewall.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure HDInsight-cluster of gegevens archieven van het cluster (Azure Storage accounts of Azure Data Lake Storage Gen1/Gen2), kunnen onderhandelen over TLS 1,2 of hoger. Microsoft Azure resources onderhandelen standaard TLS 1,2. 

Meer informatie over Azure Data Lake Storage versleuteling in transit:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Meer informatie over Azure Storage account versleuteling in transit:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: met de Enterprise Security Package van Azure HDINSIGHT (ESP) kunt u Apache zwerver gebruiken voor het maken en beheren van beleids regels voor nauw keurig toegangs beheer en gegevens opslag voor uw gegevens die zijn opgeslagen in bestanden, mappen, data bases, tabellen en rijen/kolommen. De Hadoop-beheerder kan op rollen gebaseerd toegangs beheer (RBAC) configureren om Apache Hive, HBase, Kafka en Spark te beveiligen met behulp van deze invoeg toepassingen in Apache zwerver.

Het configureren van het RBAC-beleid met Apache zwerver biedt u de mogelijkheid om machtigingen te koppelen aan een rol in de organisatie. Deze laag van abstractie maakt het gemakkelijker om ervoor te zorgen dat personen alleen de machtigingen hebben die nodig zijn om hun werk verantwoordelijkheden uit te voeren.

Enterprise Security Package configuraties met Azure Active Directory Domain Services in HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Overzicht van ENTER prise Security in azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: voor Azure HDInsight-clusters waarin gevoelige gegevens worden opgeslagen of verwerkt, markeert u het cluster en gerelateerde resources als gevoelig voor het gebruik van tags. De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: als u Azure SQL database gebruikt om Apache Hive-en Apache Oozie-meta gegevens op te slaan, zorgt u ervoor dat SQL-gegevens altijd versleuteld blijven. Voor Azure Storage accounts en Data Lake Storage (gen1 of Gen2) wordt aanbevolen micro soft toe te staan uw versleutelings sleutels te beheren, maar u hebt de mogelijkheid om uw eigen sleutels te beheren.

Versleutelings sleutels voor Azure Storage accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Azure Data Lake Storage maken met door de klant beheerde versleutelings sleutels:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Informatie over versleuteling voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Transparent Data Encryption voor SQL Database configureren met behulp van door de klant beheerde sleutels:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Richt lijnen**: Diagnostische instellingen configureren voor Azure Storage accounts die zijn gekoppeld aan Azure HDInsight-clusters om alle ruwe bewerkingen te controleren en te registreren voor cluster gegevens. Schakel controle in voor opslag accounts of Data Lake archieven die zijn gekoppeld aan het Azure HDInsight-cluster.

Aanvullende logboek registratie/controle inschakelen voor een Azure Storage account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Aanvullende logboek registratie/controle voor Azure Data Lake Storage inschakelen:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden.

Als u een abonnement op Rapid7, Qualys of een ander platform voor beveiligings problemen hebt, kunt u ook script acties gebruiken om agents voor beveiligings evaluaties te installeren op uw Azure HDInsight-cluster knooppunten en de knoop punten te beheren via de respectieve Portal.

Rapid7 agent hand matig installeren:

https://insightvm.help.rapid7.com/docs/install

Qualys agent hand matig installeren:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Script acties gebruiken:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: automatische systeem updates zijn ingeschakeld voor installatie kopieën van cluster knooppunten, maar u moet de cluster knooppunten echter periodiek opnieuw opstarten om ervoor te zorgen dat er updates worden toegepast.

Micro soft voor het onderhouden en bijwerken van basis-Azure HDInsight-knooppunt installatie kopieën.

Het patch schema voor het besturings systeem voor HDInsight-clusters configureren:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Hulp**: script acties of andere mechanismen gebruiken om uw Azure HDInsight-clusters te patchen. Nieuwe clusters hebben altijd de meest recente beschik bare updates, inclusief de meest recente beveiligings patches.

Het patch schema voor het besturings systeem configureren voor Azure HDInsight-clusters op basis van Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Script acties gebruiken:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden die de mogelijkheid biedt om beveiligings controles in de loop van de tijd te vergelijken. Als u een Rapid7-of Qualys-abonnement hebt, kunt u de portal van die leverancier gebruiken voor het weer geven en vergelijken van back-to-back-scans.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld een gemeen schappelijk score systeem voor beveiligings problemen) of de standaard risico classificaties van het hulp programma van derden.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief Azure HDInsight-clusters, binnen uw abonnement (en).  Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

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

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen uw abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: Implementeer een oplossing van derden om cluster knooppunten te bewaken voor niet-goedgekeurde software toepassingen.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief Azure HDInsight-clusters, binnen uw abonnement (en).  Verwijder alle niet-goedgekeurde Azure-resources die u ontdekt. Voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om niet-goedgekeurde software te verwijderen of te waarschuwen.

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om te voor komen dat onbevoegde software wordt uitgevoerd.


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: voor Azure HDInsight-cluster knooppunten implementeert u een oplossing van derden om te voor komen dat niet-geautoriseerde bestands typen worden uitgevoerd.


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; Dit is niet van toepassing op Azure HDInsight als gebruikers (niet-beheerders) van het cluster geen toegang nodig hebben tot de afzonderlijke knoop punten om taken uit te voeren. De Cluster beheerder heeft toegang tot het hoofd niveau voor alle cluster knooppunten.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor Azure app service-of COMPUTE-resources die webtoepassingen hosten.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om de netwerk configuratie van uw HDInsight-cluster te controleren of af te dwingen.

Beschik bare Azure Policy aliassen weer geven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: installatie kopieën van het Azure HDInsight-besturings systeem die worden beheerd en beheerd door micro soft. De klant die verantwoordelijk is voor het implementeren van beveiligde configuraties voor het besturings systeem van uw cluster knooppunten. 


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure HDInsight-clusters en gerelateerde resources.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: installatie kopieën van het Azure HDInsight-besturings systeem die worden beheerd en beheerd door micro soft. De klant die verantwoordelijk is voor het implementeren van de status configuratie op besturingssysteem niveau.


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

[Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; aangepaste installatie kopieën zijn niet van toepassing op Azure HDInsight.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Hulp**: Implementeer een oplossing van derden om de gewenste status voor de besturings systemen van het cluster knooppunt te onderhouden.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. HDInsight ' om aangepaste beleids regels te maken om de configuratie van uw HDInsight-cluster te controleren of af te dwingen.

[Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

[Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: Implementeer een oplossing van derden om de status van de cluster knooppunt besturingssystemen te bewaken.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure HDInsight bevat Bring your own Key-ondersteuning (BYOK) voor Apache Kafka. Met deze mogelijkheid kunt u de sleutels die worden gebruikt voor het versleutelen van gegevens in rust krijgen en beheren.

Alle beheerde schijven in azure HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Standaard worden de gegevens op deze schijven versleuteld met door micro soft beheerde sleutels. Als u BYOK inschakelt, geeft u de versleutelings sleutel voor Azure HDInsight op om deze te gebruiken en te beheren met Azure Key Vault.

Key Vault kunnen ook worden gebruikt met Azure HDInsight-implementaties voor het beheren van sleutels voor cluster opslag (Azure Storage accounts en Azure Data Lake Storage)

Uw eigen sleutel voor Apache Kafka in azure HDInsight zetten:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Versleutelings sleutels voor Azure Storage accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: beheerde identiteiten kunnen worden gebruikt in azure HDInsight om uw clusters toegang te geven Azure Active Directory Domain Services, toegang te krijgen tot Azure Key Vault of toegang te krijgen tot bestanden in azure data Lake Storage Gen2.

Beheerde identiteiten begrijpen met Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: als u code gebruikt die betrekking heeft op uw Azure HDInsight-implementatie, kunt u referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

Referentie scanner instellen:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën, maar u moet de software echter wel beheren en hand matig aggregatie of bewakings logboeken clamscan genereren.

Meer informatie over clamscan voor Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf bestanden die worden geüpload naar Azure-resources die zijn gerelateerd aan uw Azure HDInsight-cluster implementatie, zoals Data Lake Storage, Blob Storage, enzovoort. Micro soft heeft geen toegang tot klant gegevens in deze instanties.

Meer informatie over micro soft antimalware voor Azure Cloud Services en Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Hulp**: Azure HDInsight wordt geleverd met clamscan vooraf geïnstalleerd en ingeschakeld voor de cluster knooppunt installatie kopieën. Clamscan voert automatisch engine-en definitie-updates uit, maar aggregatie en beheer van Logboeken moet hand matig worden uitgevoerd.

Inzicht in clamscan voor Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: als u een Azure Storage-account gebruikt voor het gegevens archief van het HDInsight-cluster, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS).  Wanneer u een Azure SQL Database gebruikt voor het Azure HDInsight-cluster gegevens archief, configureert u actieve geo-replicatie.

Opslag redundantie configureren voor Azure Storage accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Redundantie voor Azure SQL Database configureren:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Richt lijnen**: als u een Azure Storage-account gebruikt voor de gegevens opslag van het Azure HDInsight-cluster, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS). Als u Azure Key Vault gebruikt voor elk onderdeel van uw Azure HDInsight-implementatie, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

Kies opslag opties voor uw Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Opslag redundantie configureren voor Azure Storage accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Een back-up maken van Key Vault sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als Azure Key Vault wordt gebruikt in combi natie met uw Azure HDInsight-implementatie, test u het herstellen van back-ups van door de klant beheerde sleutels.

Uw eigen sleutel voor Apache Kafka in azure HDInsight zetten:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Sleutel kluis sleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als Azure Key Vault wordt gebruikt in combi natie met uw Azure HDInsight-implementatie, schakelt u Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Soft-Delete in Azure Key Vault inschakelen:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richt lijnen**: Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die rollen van personeel en fasen van incident handling/Management definiëren.

Werk stroom automatisering configureren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema. Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

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

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen, hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)