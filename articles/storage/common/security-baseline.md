---
title: Azure-beveiligingsbasislijn voor Azure Storage
description: De Azure Storage beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589445"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-beveiligingsbasislijn voor Azure Storage

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0](../../security/benchmarks/overview-v1.md) toe op Azure Storage. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security-benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Storage. **Besturingselementen** die niet van Azure Storage zijn uitgesloten.

 
Zie het volledige Azure Storage toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Storage volledig is toegewezen aan de Azure Security [Azure Storage-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Configureer de firewall van uw opslagaccount door de toegang tot clients vanuit specifieke openbare IP-adresbereiken te beperken, virtuele netwerken of specifieke Azure-resources te selecteren.  U kunt ook privé-eindpunten configureren, zodat verkeer naar de opslagservice van uw onderneming uitsluitend via particuliere netwerken wordt verplaatst.

Opmerking: klassieke opslagaccounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

- [De firewall voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Privé-eindpunten configureren voor Azure Storage](storage-private-endpoints.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** Azure Storage biedt een gelaagd beveiligingsmodel. U kunt de toegang tot uw opslagaccount beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-bereiken of van een lijst met subnetten in een Azure-Virtual Network. U kunt de Azure Security Center en de aanbevelingen voor netwerkbeveiliging volgen om uw netwerkbronnen in Azure te beveiligen. Schakel ook stroomlogboeken voor netwerkbeveiligingsgroep in voor virtuele netwerken of subnetten die zijn geconfigureerd voor de opslagaccounts via de firewall van het opslagaccount en verzend logboeken naar een opslagaccount voor verkeerscontrole. 

 
Als er privé-eindpunten zijn gekoppeld aan uw opslagaccount, kunt u geen regels voor netwerkbeveiligingsgroep configureren voor subnetten. 
 

 
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md) 
 

 
- [NSG-stroomlogboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Informatie over privé-eindpunten voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** schakel Azure Defender opslag in voor uw Azure Storage account. Azure Defender for Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen tot het openen of exploiteren van opslagaccounts worden gedetecteerd.  Azure Security Center geïntegreerde waarschuwingen zijn gebaseerd op activiteiten waarvoor netwerkcommunicatie is gekoppeld aan een IP-adres dat is opgelost, ongeacht of het IP-adres een bekend riskant IP-adres is (bijvoorbeeld een bekende cryptominer) of een IP-adres dat eerder niet als riskant is herkend. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. 

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md) 

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** Network Watcher pakketopname kunt u opnamesessies maken om het verkeer tussen het opslagaccount en een virtuele machine bij te houden. Er worden filters opgegeven voor de opnamesessie om ervoor te zorgen dat u alleen het wantverkeer vast leggen. Pakketopname helpt bij het diagnosticeren van netwerkafwijkingen, zowel reactief als proactief. Andere toepassingen zijn onder andere het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkindringingen, het opsporen van fouten in client-servercommunicatie en nog veel meer. Door pakketopnamen op afstand te activeren, kunt u een pakketopname handmatig uitvoeren op een gewenste virtuele machine, waardoor kostbare tijd wordt bespaard. 

- [ Pakketopnamen beheren met Azure Network Watcher met behulp van de portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Azure Defender storage biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen tot toegang tot of aanvallen op opslagaccounts detecteert. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligingswaarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

- [Azure Defender voor Storage configureren](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** voor resources in virtuele netwerken die toegang nodig hebben tot uw opslagaccount, gebruikt u Virtual Network Service-tags voor de geconfigureerde Virtual Network voor het definiëren van besturingselementen voor netwerktoegang in netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (zoals Opslag) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen. 

Wanneer netwerktoegang moet worden beperkt tot specifieke opslagaccounts, gebruikt u Virtual Network beleid voor service-eindpunten.

- [Voor meer informatie over het gebruik van servicetags](../../virtual-network/service-tags-overview.md)

- [Voor meer informatie over service-eindpuntbeleid voor virtuele netwerken voor Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die zijn gekoppeld aan uw Azure Storage-account met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten Microsoft.Storage en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw opslagaccountresources te controleren of af te dwingen. 

U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op een opslagaccount, zoals: Opslagaccounts moeten gebruikmaken van een service-eindpunt voor een virtueel netwerk 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy voor Storage](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Azure Policy voorbeelden voor Network](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Een Azure Blueprint maken](../../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Met taggen kunt u ingebouwde en aangepaste naam-waardeparen koppelen aan een bepaalde netwerkresource, zodat u netwerkresources kunt organiseren en Azure-resources weer kunt koppelen aan uw netwerkontwerp.

Gebruik een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag vereisen en de waarde ervan, om ervoor te zorgen dat alle resources worden gemaakt met tags en om u op de hoogte te stellen van bestaande resources zonder tag. 

Netwerkbeveiligingsgroepen ondersteunen tags, maar afzonderlijke beveiligingsregels niet. Beveiligingsregels hebben wel een **veld** Beschrijving dat u kunt gebruiken om een deel van de informatie op te slaan die u normaal gesproken in een tag zou zetten.

- [Resourcetags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Netwerkverkeer filteren met een netwerkbeveiligingsgroep](../../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** gebruik Azure Policy om configuratiewijzigingen voor netwerkbronnen te logboeken.  Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.  

 
- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Waarschuwingen maken in Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen via Azure Monitor voor het verzamelen van beveiligingsgegevens die zijn gegenereerd door eindpuntapparaten, netwerkbronnen en andere beveiligingssystemen. Gebruik Azure Monitor Log Analytics-werkruimte(s) om query's uit te voeren en analyses uit te voeren, en gebruik Azure Storage-accounts voor langetermijn-/archiveringsopslag, eventueel met beveiligingsfuncties zoals onveranderbare opslag en afgedwongen bewaarperioden.

 
- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Azure Opslaganalyse logboeken voor blobs, wachtrijen en tabellen. U kunt de Azure Portal om te configureren welke logboeken worden vastgelegd voor uw account.   

 
- [Bewaking configureren voor uw Azure Storage account](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** wanneer u beveiligingsgebeurtenislogboeken opslaat in het Azure Storage-account of log analytics-werkruimte, kunt u het bewaarbeleid instellen op basis van de vereisten van uw organisatie.  

 
- [Retentiebeleid configureren voor Azure Storage accountlogboeken](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [De gegevensretentieperiode in Log Analytics wijzigen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** voor het Azure Storage logboeken zijn er de gebruikelijke opties, zoals query's via de Log Analytics-aanbieding, evenals een unieke optie om de logboekbestanden rechtstreeks weer te geven. In Azure Storage worden de logboeken opgeslagen in blobs die rechtstreeks moeten worden gebruikt in (de map voor logboekregistratie is standaard verborgen, dus u moet rechtstreeks `http://accountname.blob.core.windows.net/$logs` navigeren. Deze wordt niet weergegeven in lijstopdrachten) 

 
Schakel ook Azure Defender voor Storage in voor uw opslagaccount. Azure Defender for Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen tot het openen of exploiteren van opslagaccounts worden gedetecteerd. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligingswaarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 
 

 
- [Gegevens in een logboek opslaan en controleren](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Azure Defender voor Storage configureren](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** schakel in Azure Security Center opslagaccount Azure Defender opslagaccount in. Schakel diagnostische instellingen in voor het opslagaccount en verzend logboeken naar een Log Analytics-werkruimte. Onboard uw Log Analytics-werkruimte voor Azure Sentinel, omdat deze een SOAR-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligingsproblemen op te lossen.  

 
- [Onboarding van Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Waarschuwingen beheren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Waarschuwingen voor logboekgegevens van Log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage-analyselogboeken](storage-analytics-logging.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** Gebruik Azure Security Center en schakel Azure Defender for Storage in voor het detecteren van malware-uploads naar Azure Storage met behulp van hashreputatieanalyse en verdachte toegang vanaf een actief Tor-exit-knooppunt (een anonieme proxy). 

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Azure DNS Analytics-oplossing (preview) in Azure Monitor inzicht in de DNS-infrastructuur voor beveiliging, prestaties en bewerkingen.  Dit biedt momenteel geen ondersteuning voor Azure Storage accounts, maar u kunt de dns-logboekregistratieoplossing van derden gebruiken.  

 
- [Inzichten verzamelen over uw DNS-infrastructuur met de DNS-analyse Preview-oplossing](../../azure-monitor/insights/dns-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark:](../../security/benchmarks/security-control-identity-access-control.md)identiteit en Access Control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Storage accounts Azure Active Directory (Azure AD) het concept standaardwachtwoorden of lege wachtwoorden hebben. Azure Storage implementeert een model voor toegangsbeheer dat ondersteuning biedt voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC), evenals shared key en Shared Access Signatures (SAS). Een kenmerk van gedeelde sleutel- en SAS-verificatie is dat er geen identiteit is gekoppeld aan de aanroeper en daarom geen autorisatie op basis van beveiligingsprincipalmachtigingen kan worden uitgevoerd.

- [Toegang tot gegevens in Azure Storage](storage-auth.md)

- [Informatie over beveiligingsprincipa en toegangsbeheer voor Azure Storage account](storage-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** maak standaardprocedures voor het gebruik van toegewezen beheerdersaccounts die toegang hebben tot uw Opslagaccount. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

U kunt ook Just-In-Time/Just-Enough-Access inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services en Azure Resource Manager.

- [Inzicht Azure Security Center identiteit en toegang](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management Overzicht](/azure/active-directory/privileged-identity-management/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** gebruik waar mogelijk eenmalige Azure Active Directory (Azure AD) in plaats van afzonderlijke, afzonderlijke referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer.
 

 
- [Inzicht in eenmalige aanmelding met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Toegang tot gegevens in een Azure Storage](storage-auth.md)
 

 
- [Toegang verlenen tot blobs en wachtrijen met behulp van Azure AD](storage-auth-aad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en aanbevelingen voor Azure Security Center Identity and Access Management volgen om uw Storage-accountresources te beveiligen.

- [Meervoudige verificatie inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** Gebruik PAW's (privileged access-werkstations) met meervoudige verificatie die is geconfigureerd om u aan te melden bij opslagaccountresources en deze te configureren.  

 
- [Meer informatie over Privileged Access-werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Meervoudige verificatie inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** waarschuwingen Azure Security Center risicodetectie verzenden naar Azure Monitor configureren van aangepaste waarschuwingen/meldingen met behulp van actiegroepen. Schakel Azure Defender opslagaccount in om waarschuwingen voor verdachte activiteiten te genereren. Gebruik daarnaast risicodetecties voor Azure Active Directory (Azure AD) om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)
 

- [Inzicht in Azure AD-risicodetecties](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Actiegroepen configureren voor aangepaste waarschuwingen en meldingen](../../azure-monitor/alerts/action-groups.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's. 

- [Benoemde locaties configureren in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure biedt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor een fijner beheer van de toegang van een client tot resources in een opslagaccount.   Gebruik waar mogelijk Azure AD-referenties als best practice, in plaats van de accountsleutel te gebruiken, die gemakkelijker kan worden aangetast. Wanneer uw toepassingsontwerp handtekeningen voor gedeelde toegang vereist voor toegang tot Blob Storage, gebruikt u Azure AD-referenties om waar mogelijk een SAS (User Delegation Shared Access Signatures) te maken voor betere beveiliging.

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [De Azure Storage gebruiken voor toegang tot beheerresources](authorization-resource-provider.md)

- [Toegang tot Azure Blob- en Queue-gegevens configureren met Azure RBAC in Azure Portal](storage-auth-aad-rbac-portal.md)

- [Toegang tot gegevens in Azure Storage](storage-auth.md)

- [Beperkte toegang verlenen tot Azure Storage resources met behulp van Shared Access Signatures (SAS)](storage-sas-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Bekijk de logboeken Azure Active Directory (Azure AD) om verouderde accounts te ontdekken, waaronder accounts met beheerdersrollen voor opslagaccounts. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen die kunnen worden gebruikt voor toegang tot opslagaccountbronnen en roltoewijzingen, efficiënt te beheren. Gebruikerstoegang moet regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 
 

 
U kunt ook Shared Access Signature (SAS) gebruiken om beveiligde gedelegeerde toegang tot resources in uw opslagaccount te bieden zonder dat dit ten koste gaat van de beveiliging van uw gegevens. U kunt bepalen tot welke resources de client toegang heeft, welke machtigingen ze hebben voor deze resources en hoe lang de SAS geldig is, en andere parameters.
 

 
Bekijk ook anonieme leestoegang tot containers en blobs. De standaardinstelling is dat een container en alle bijbehorende blobs alleen toegankelijk zijn voor een gebruiker die over de juiste machtigingen beschikt. U kunt deze Azure Monitor voor anonieme toegang voor opslagaccounts met behulp van anonieme verificatievoorwaarde.
 

 
Een effectieve manier om het risico van niet-geïnspecteerde toegang tot gebruikersaccounts te verminderen, is door de duur van toegang die u aan gebruikers verleent te beperken. Tijdelijke SAS-URI's zijn een effectieve manier om gebruikerstoegang tot een opslagaccount automatisch te laten verlopen. Daarnaast is het regelmatig roteren van opslagaccountsleutels een manier om ervoor te zorgen dat onverwachte toegang via opslagaccountsleutels een beperkte duur heeft.
 

 
- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/) 
 

 
- [Toegang weergeven en wijzigen op Azure Storage accountniveau](storage-auth-aad-rbac-portal.md)
 

 
- [Beperkte toegang verlenen tot Azure Storage resources met behulp van Shared Access Signatures (SAS)](storage-sas-overview.md)
 

 
- [Anonieme leestoegang tot containers en blobs beheren](../blobs/anonymous-read-access-configure.md)
 

 
- [Een Storage-account bewaken in de Azure-portal](manage-storage-analytics-logs.md)
 

 
- [Toegangssleutels voor opslagaccounts beheren](storage-account-keys-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** gebruik Opslaganalyse om gedetailleerde informatie te verzamelen over geslaagde en mislukte aanvragen bij een opslagservice. Alle logboeken worden opgeslagen in blok-blobs in een container met de naam $logs, die automatisch worden gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslagaccount.
 

Maak diagnostische instellingen Azure Active Directory gebruikersaccounts (Azure AD) en verstuur de auditlogboeken en aanmeldingslogboeken naar een Log Analytics-werkruimte. U kunt de gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

Als u verificatiefouten wilt controleren Azure Storage accounts, kunt u waarschuwingen maken om u te waarschuwen wanneer bepaalde drempelwaarden zijn bereikt voor metrische gegevens van opslagresources. Gebruik daarnaast een Azure Monitor te waarschuwen bij anonieme toegang voor opslagaccounts met behulp van de anonieme verificatievoorwaarde.

- [Azure Storage-analyselogboeken](storage-analytics-logging.md)
 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Waarschuwingen voor metrische gegevens configureren voor Azure Storage Accounts](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-functies voor risico- en identiteitsbeveiliging om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot uw opslagaccountbronnen. U moet geautomatiseerde reacties inschakelen via Azure Sentinel beveiligingsreacties van uw organisatie te implementeren.

- [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang moet hebben tot klantgegevens, biedt Klanten-lockbox (preview voor opslagaccount) een interface voor klanten om aanvragen voor toegang tot klantgegevens te controleren en goed te keuren of af te wijzen. Microsoft vereist en vraagt geen toegang aan tot de geheimen van uw organisatie die zijn opgeslagen in het opslagaccount.

- [Meer Klanten-lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om te helpen bij het bijhouden van opslagaccountbronnen die gevoelige informatie opslaan of verwerken. 

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Isolatie implementeren met behulp van afzonderlijke abonnementen, beheergroepen en opslagaccounts voor afzonderlijke beveiligingsdomeinen, zoals omgeving en gegevensgevoeligheid.  U kunt uw opslagaccount beperken om het toegangsniveau te bepalen tot uw opslagaccounts dat uw toepassingen en bedrijfsomgevingen nodig hebben, op basis van het type en de subset van de gebruikte netwerken. Wanneer netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot een opslagaccount. U kunt de toegang tot Azure Storage via Azure RBAC (Azure RBAC).

U kunt ook privé-eindpunten configureren om de beveiliging te verbeteren wanneer verkeer tussen uw virtuele netwerk en de service via het backbone-netwerk van Microsoft gaat, waardoor de blootstelling van het openbare internet wordt voorkomen.

- [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)

- [Service-eindpunten voor virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** Markeer de resources als gevoelig voor opslagaccountbronnen die gevoelige informatie opslaan of verwerken met tags. Om het risico op gegevensverlies via exfiltratie te verminderen, beperkt u uitgaand netwerkverkeer voor Azure Storage accounts met behulp Azure Firewall.  

Gebruik daarnaast beleidsregels voor service-eindpunten voor virtuele netwerken om het verkeer van het virtuele netwerk te filteren op Azure Storage-accounts via service-eindpunten en gegevens exfiltratie alleen toe te staan voor specifieke Azure Storage-accounts.

- [Azure Storage-firewalls en virtuele netwerken configureren](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Service-eindpuntbeleid voor virtueel netwerk voor Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** u kunt het gebruik van HTTPS afdwingen door veilige overdracht in te stellen die vereist is voor het opslagaccount. Als u deze optie inschakelt, worden verbindingen via HTTP geweigerd.  Gebruik daarnaast Azure Security Center en Azure Policy veilige overdracht af te dwingen voor uw opslagaccount.

- [Veilige overdracht vereisen in Azure Storage](storage-require-secure-transfer.md)

- [Azure-beveiligingsbeleid dat wordt bewaakt door Security Center](../../security-center/policy-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Gegevensidentificatiefuncties zijn nog niet beschikbaar voor Azure Storage account en gerelateerde resources. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden. 

- [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Azure Active Directory (Azure AD) autoreert toegangsrechten voor beveiligde resources via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Azure Storage definieert een set ingebouwde Azure RBAC-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot blob- of wachtrijgegevens.

- [Azure RBAC-rollen toewijzen voor Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [De Azure Storage gebruiken voor toegang tot beheerresources](authorization-resource-provider.md)

- [Toegang tot Azure Blob- en Queue-gegevens configureren met Azure RBAC in Azure Portal](storage-auth-aad-rbac-portal.md)

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Toegang tot gegevens in Azure Storage](storage-auth.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Azure Storage versleuteling is ingeschakeld voor alle opslagaccounts en kan niet worden uitgeschakeld. Azure Storage versleutelt uw gegevens automatisch wanneer deze worden opgeslagen in de cloud. Wanneer u gegevens uit Azure Storage leest, worden deze door Azure Storage ontsleuteld voordat ze worden geretourneerd. Azure Storage versleuteling kunt u uw data-at-rest beveiligen zonder dat u code moet wijzigen of code aan toepassingen moet toevoegen. 

- [Meer informatie Azure Storage versleuteling in rust](storage-service-encryption.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in opslagaccountbronnen.  U kunt ook logboekregistratie Azure Storage om bij te houden hoe elke aanvraag is Azure Storage is geautoriseerd. De logboeken geven aan of een aanvraag anoniem is ingediend, met behulp van een OAuth 2.0-token, met behulp van een gedeelde sleutel of met behulp van een Shared Access Signature (SAS).  Gebruik daarnaast Azure Monitor voor anonieme toegang voor opslagaccounts met behulp van anonieme verificatievoorwaarde. 

 
- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage-analyselogboeken](storage-analytics-logging.md) 

 
- [Waarschuwingen voor metrische gegevens configureren voor Azure Storage Accounts](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** volg de aanbevelingen van Azure Security Center om de configuratie van uw opslagaccounts continu te controleren en te controleren.  

- [Aanbevelingen voor beveiliging: een naslaggids](../../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Niet van toepassing; Microsoft voert vulnerability management uit op de onderliggende systemen die ondersteuning bieden voor opslagaccounts.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicoclassificaties (Secure Score) die door de Azure Security Center. 

- [Inzicht in Azure Security Center secure score](/azure/security-center/security-center-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (inclusief opslagaccounts) binnen uw abonnement(en) op te vragen en te ontdekken. Zorg ervoor dat u over de juiste machtigingen (lezen) in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

- [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht in Azure RBAC](../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op opslagaccountbronnen, zodat metagegevens worden gegeven om ze logisch te ordenen in een taxonomie. 

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om opslagaccounts en gerelateerde resources te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit het abonnement op tijd. 

Gebruik ook Azure Defender voor Storage om niet-geautoriseerde Azure-resources te detecteren. 

- [Extra Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md) 

- [Een Beheergroepen](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** u moet een inventaris maken van goedgekeurde Azure-resources op de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities: 

 
 - Niet toegestane resourcetypen 
 
 - Toegestane brontypen 
 

 
Gebruik daarnaast de Azure Resource Graph om resources in de abonnementen op te vragen en te ontdekken. Dit kan helpen bij omgevingen met een hoge beveiliging, zoals omgevingen met opslagaccounts. 
 

 
- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** De klant kan het maken of gebruiken van resources Azure Policy zoals vereist door het bedrijfsbeleid van de klant. 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy ingebouwde definities - Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik de voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management. Dit kan voorkomen dat er resources worden gemaakt en gewijzigd binnen een omgeving met een hoge beveiliging, zoals resources met opslagaccounts. 

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy-aliassen in de **naamruimte Microsoft.Storage** om aangepaste beleidsregels te maken om de configuratie van uw opslagaccount-exemplaren te controleren of af te dwingen. U kunt ook ingebouwde definities Azure Policy voor Azure Storage account gebruiken, zoals:

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Een Azure Defender voor Storage implementeren
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld

Gebruik aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw opslagaccounts.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw opslagaccountbronnen. 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 

- [Inzicht Azure Policy effecten](../../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure-Desired State Configuration voor het veilig opslaan en beheren van uw code, zoals aangepaste Azure-beleidsregels, Azure Resource Manager-sjablonen, Desired State Configuration scripts, enzovoort. Als u toegang wilt krijgen tot de resources die u beheert in Azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Azure AD als deze zijn geïntegreerd met TFS.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** Maak gebruik Azure Policy om systeemconfiguraties voor opslagaccounts te waarschuwen, controleren en afdwingen. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen. 

- [ Gegevens configureren en beheren Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** Gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure Storage-accountbronnen. 

- [Aanbevelingen herstellen in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Azure Storage uw gegevens automatisch versleutelen wanneer deze naar de cloud worden opgeslagen. U kunt door Microsoft beheerde sleutels gebruiken voor de versleuteling van het opslagaccount of versleuteling beheren met hun eigen sleutels. Als u door de klant geleverde sleutels gebruikt, kunt u gebruikmaken Azure Key Vault sleutels veilig op te slaan. 

Daarnaast moet u opslagaccountsleutels regelmatig roteren om de impact van verlies of openbaarmaking van opslagaccountsleutels te beperken.

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)

- [Toegangssleutels voor opslagaccounts beheren](storage-account-keys-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Toegang verlenen tot blobs en wachtrijen binnen Azure Storage Accounts met Azure Active Directory (Azure AD) en beheerde identiteiten. Azure Blob- en Queue Storage ondersteunen Azure AD-verificatie met beheerde identiteiten voor Azure-resources. 

Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot blob- en wachtrijgegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in Azure Virtual Machines r, functie-apps, virtuele-machineschaalsets en andere services. Door beheerde identiteiten voor Azure-resources samen met Azure AD-verificatie te gebruiken, kunt u voorkomen dat referenties worden opgeslagen bij uw toepassingen die in de cloud worden uitgevoerd.

- [Toegang verlenen tot Azure Blob- en Queue-gegevens met behulp van een beheerde identiteit](storage-auth-aad-rbac-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [ Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden die moeten worden geüpload naar niet-compute Azure-resources vooraf scannen

**Richtlijnen:** gebruik Azure Defender for Storage voor het detecteren van malware-uploads naar Azure Storage met behulp van hashreputatieanalyse en verdachte toegang vanaf een actief Tor exit-knooppunt (een anonieme proxy). 
 

 
U kunt ook alle inhoud op malware vooraf scannen voordat u uploadt naar niet-compute Azure-resources, zoals App Service, Data Lake Storage, Blob Storage en andere, om te voldoen aan de vereisten van uw organisatie.
 

 
- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** de gegevens in Microsoft Azure opslagaccount worden altijd automatisch gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Azure Storage kopieert uw gegevens zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomstoringen en grote natuurrampen. U kunt ervoor kiezen om uw gegevens te repliceren binnen hetzelfde datacenter, in meerdere datacenters binnen dezelfde regio of in geografisch gescheiden regio's. 

U kunt Azure Automation ook inschakelen om regelmatig momentopnamen van de blobs te maken.

- [Inzicht Azure Storage redundantie en Service-Level overeenkomsten](storage-redundancy.md)

- [Een momentopname van een blob maken](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Overzicht van Azure Automation](../../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** als u een back-up wilt maken van gegevens uit door opslagaccounts ondersteunde services, zijn er meerdere methoden beschikbaar, waaronder het gebruik van azcopy of hulpprogramma's van derden. Met onveranderbare opslag voor Azure Blob Storage kunnen gebruikers bedrijfskritieke gegevensobjecten opslaan in een WORM-status (Write Once, Read Many). Deze status maakt de gegevens voor een door de gebruiker opgegeven interval niet-wissend en niet-wijzigbaar.
 

Door de klant beheerde/geleverde sleutels kunnen binnen een Azure Key Vault met behulp van Azure CLI of PowerShell. 

 
- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)  

- [Beleid voor onveranderbaarheid instellen en beheren voor Blob Storage](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** voer periodiek gegevensherstel uit Key Vault certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Uw certificaten Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [De sleutels Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Beheerde Key Vault herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Uw geheimen Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs, bestanden en tabelgegevens te kopiëren van of naar een opslagaccount](storage-use-azcopy-v10.md)

Opmerking: als u gegevens wilt kopiëren van en naar uw Azure Table Storage-service, installeert u AzCopy versie 7.3.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** als u door de klant beheerde sleutels voor een opslagaccount wilt inschakelen, moet u een Azure Key Vault sleutels opslaan. U moet zowel de eigenschappen Soft Delete als Do Not Purge inschakelen voor de sleutelkluis.  Key Vault functie voor het verwijderen van gegevens kunt u verwijderde kluizen en kluisobjecten, zoals sleutels, geheimen en certificaten, herstellen.  Als u een back-Azure Storage opslagaccountgegevens naar blobs, moet u de functie voor het opslaan en herstellen van uw gegevens inschakelen wanneer blobs of blob-momentopnamen worden verwijderd.  U moet uw back-ups behandelen als gevoelige gegevens en de relevante besturingselementen voor toegangs- en gegevensbeveiliging toepassen als onderdeel van deze basislijn.  Daarnaast kunt u voor verbeterde beveiliging bedrijfskritieke gegevensobjecten opslaan in een WORM-status (Eenmaal schrijven, Veel lezen).

- [Het gebruik van Azure Key Vault's voor het verwijderen van gegevens](../../key-vault/general/key-vault-recovery.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/soft-delete-blob-overview.md)

- [Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](../blobs/storage-blob-immutable-storage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST's Computer Security Incident Handling Guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit is die heeft geleid tot de waarschuwing. 

 
Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) met behulp van tags en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren, met name de resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.
 

 
- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)
 

 
- [Tags gebruiken om Azure-resources te organiseren](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.
    

- [ Werkstroomautomatisering en -Logic Apps](../../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
