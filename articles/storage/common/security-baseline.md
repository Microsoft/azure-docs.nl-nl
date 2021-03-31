---
title: Azure-beveiligings basislijn voor Azure Storage
description: De Azure Storage Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 56f340a8d64346fd8934e9cfbae26079d4ee7f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576535"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-beveiligings basislijn voor Azure Storage

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../../security/benchmarks/overview-v1.md) ingesteld op Azure Storage. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Storage. **Besturings elementen** die niet van toepassing zijn op Azure Storage, zijn uitgesloten.

 
Als u wilt zien hoe Azure Storage volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Storage beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Configureer de firewall van uw opslag account door de toegang te beperken tot clients van specifieke open bare IP-adresbereiken, virtuele netwerken of specifieke Azure-resources te selecteren.  U kunt ook privé-eind punten configureren, zodat het verkeer naar de opslag service van uw bedrijf uitsluitend via particuliere netwerken wordt verplaatst.

Opmerking: klassieke opslag accounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

- [De Azure Storage firewall configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Privé-eind punten voor Azure Storage configureren](storage-private-endpoints.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: Azure Storage biedt een gelaagd beveiligings model. U kunt de toegang tot uw opslag account beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-adresbereiken of uit een lijst met subnetten in een Azure-Virtual Network. U kunt Azure Security Center gebruiken en aanbevelingen voor netwerk beveiliging volgen om uw netwerk bronnen in azure te beveiligen. Schakel ook stroom logboeken voor netwerk beveiligings groepen in voor virtuele netwerken of subnet dat is geconfigureerd voor de opslag accounts via de firewall van het opslag account en logboeken verzenden naar een opslag account voor verkeers controle. 

 
Als u privé-eind punten aan uw opslag account hebt gekoppeld, kunt u de regels voor de netwerk beveiligings groep voor subnetten niet configureren. 
 

 
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md) 
 

 
- [NSG-stroom logboeken inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Informatie over netwerk beveiliging die wordt verschaft door Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Meer informatie over privé-eind punten voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure Defender in voor opslag voor uw Azure Storage-account. Azure Defender for Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen tot het openen of exploiteren van opslagaccounts worden gedetecteerd.  Azure Security Center geïntegreerde waarschuwingen zijn gebaseerd op activiteiten waarvoor netwerk communicatie is gekoppeld aan een IP-adres dat is omgezet, ongeacht of het IP-adres een bekend riskant IP-adres is (bijvoorbeeld een bekend cryptominer) of een IP-adres dat niet eerder is herkend als riskant. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. 

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md) 

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: bij het vastleggen van Network Watcher-pakketten kunt u opname sessies maken om verkeer tussen het opslag account en een virtuele machine bij te houden. Er worden filters voor de opname sessie gegeven om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Met pakket opname kunt u netwerk afwijkingen, zowel reactief als proactief, vaststellen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het verkrijgen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en nog veel meer. Als u pakket opnames op afstand wilt activeren, vereenvoudigt u de belasting van het hand matig uitvoeren van een pakket opname op een gewenste virtuele machine, waardoor kost bare tijd wordt bespaard. 

- [ Pakket opnames beheren met Azure Network Watcher met behulp van de portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Azure Defender voor opslag biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligingswaarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

- [Azure Defender voor Storage configureren](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bron in virtuele netwerken die toegang nodig hebben tot uw opslag account, gebruikt u Virtual Network-Service Tags voor de geconfigureerde Virtual Network om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (zoals opslag) in het juiste bron-of doel veld van een regel op te geven, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

Gebruik Virtual Network service-eindpunt beleid als het bereik van netwerk toegang moet worden toegewezen aan specifieke opslag accounts.

- [Voor meer informatie over het gebruik van service Tags](../../virtual-network/service-tags-overview.md)

- [Voor meer informatie over het beleid voor service-eind punten van virtuele netwerken voor Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die zijn gekoppeld aan uw Azure Storage-Account met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. Storage ' en ' micro soft. Network ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van de resources van uw opslag account. 

U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot het opslag account, zoals: opslag accounts moeten een service-eind punt van een virtueel netwerk gebruiken 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 

- [Voor beelden Azure Policy voor opslag](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Azure Policy voor beelden voor netwerk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Een Azure Blueprint maken](../../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Met code ring kunt u ingebouwde en aangepaste naam/waarde-paren koppelen aan een bepaalde netwerk bron, zodat u netwerk bronnen kunt ordenen en Azure-resources opnieuw kunt koppelen aan uw netwerk ontwerp.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources. 

Netwerk beveiligings groepen ondersteunen Tags, maar afzonderlijke beveiligings regels niet. Beveiligings regels hebben een **beschrijvings** veld dat u kunt gebruiken voor het opslaan van een deel van de gegevens die u normaal gesp roken in een tag zou plaatsen.

- [Resource Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Netwerk verkeer filteren met een netwerk beveiligings groep](../../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik Azure Policy om configuratie wijzigingen voor netwerk bronnen te registreren.  Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.  

 
- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Waarschuwingen maken in Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die worden gegenereerd door eind punten, netwerk bronnen en andere beveiligings systemen. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en te analyseren en Azure Storage-accounts te gebruiken voor lange termijn/archief opslag, eventueel met beveiligings functies zoals onveranderlijke opslag en afgedwongen Bewaar perioden.

 
- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure Opslaganalyse biedt logboeken voor blobs, wacht rijen en tabellen. U kunt de Azure Portal gebruiken om te configureren welke logboeken voor uw account worden vastgelegd.   

 
- [Bewaking configureren voor uw Azure Storage-account](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: wanneer u beveiligings logboeken opslaat in het Azure Storage-account of log Analytics-werk ruimte, kunt u het Bewaar beleid instellen op basis van de vereisten van uw organisatie.  

 
- [Bewaar beleid configureren voor logboeken van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [De Bewaar periode voor gegevens wijzigen in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: als u de Azure Storage-logboeken wilt bekijken, zijn er de gebruikelijke opties zoals query's via de log Analytics-aanbieding en een unieke optie om de logboek bestanden rechtstreeks weer te geven. In Azure Storage worden de Logboeken opgeslagen in blobs die rechtstreeks toegankelijk moeten zijn op `http://accountname.blob.core.windows.net/$logs` (de map voor logboek registratie is standaard verborgen, dus u moet rechtstreeks door de gegevens Blade navigeren. Het wordt niet weer gegeven in lijst opdrachten.) 

 
Schakel ook Azure Defender in voor opslag voor uw opslag account. Azure Defender for Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen tot het openen of exploiteren van opslagaccounts worden gedetecteerd. Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligingswaarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 
 

 
- [Gegevens registreren en controleren](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Azure Defender voor Storage configureren](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: schakel in azure Security Center Azure Defender in voor het opslag account. Schakel de diagnostische instellingen voor het opslag account in en verzend logboeken naar een Log Analytics-werk ruimte. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.  

 
- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)  

 
- [Waarschuwingen beheren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Een waarschuwing over logboek gegevens van log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage-analyselogboeken](storage-analytics-logging.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Hulp**: Azure Security Center gebruiken en Azure Defender voor opslag inschakelen voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actief Tor-eind knooppunt (een anoniem-proxy). 

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: de oplossing Azure DNS Analytics (preview) in azure monitor verzamelt inzicht in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen.  Op dit moment wordt er geen ondersteuning geboden voor Azure Storage-accounts, maar u kunt wel gebruikmaken van de oplossing voor DNS-logboek registratie van derden.  

 
- [Inzichten over uw DNS-infra structuur verzamelen met de preview-oplossing van DNS-analyse](../../azure-monitor/insights/dns-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Storage accounts of Azure Active Directory (Azure AD) hebben het concept van standaard-of lege wacht woorden. Azure Storage implementeert een model voor toegangs beheer dat ondersteuning biedt voor op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure, evenals gedeelde sleutel en Shared Access signatures (SAS). Een kenmerk van gedeelde sleutel en SAS-verificatie is dat er geen identiteit aan de oproepende functie is gekoppeld en daarom niet kan worden gemachtigd om op basis van een machtiging voor beveiliging-principal een autorisatie uit te voeren.

- [Toegang tot gegevens in Azure Storage autoriseren](storage-auth.md)

- [Informatie over beveiligings-principals en toegangs beheer voor Azure Storage account](storage-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts die toegang hebben tot uw opslag account. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

U kunt ook Just-in-time/alleen-voldoende toegang inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure Resource Manager.

- [Inzicht in Azure Security Center identiteit en toegang](../../security-center/security-center-identity-access.md)

- [Overzicht van Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory (Azure AD) SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.
 

 
- [Informatie over eenmalige aanmelding met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Toegang tot gegevens in Azure Storage autoriseren](storage-auth.md)
 

 
- [Toegang verlenen tot blobs en wacht rijen met behulp van Azure AD](storage-auth-aad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer om de resources van uw opslag account te beveiligen.

- [Multi-factor Authentication inschakelen in azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met multi-factor Authentication die is geconfigureerd voor aanmelding bij en configureren van opslag account resources.  

 
- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Multi-factor Authentication inschakelen in azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: verzend waarschuwingen voor Azure Security Center risico detectie naar Azure monitor en configureer aangepaste waarschuwingen/meldingen met behulp van actie groepen. Schakel Azure Defender voor opslag account in om waarschuwingen te genereren voor verdachte activiteiten. Gebruik daarnaast Azure Active Directory-risico detecties (Azure AD) om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)
 

- [Meer informatie over Azure AD-risico detectie](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](../../azure-monitor/alerts/action-groups.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's. 

- [Benoemde locaties configureren in azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure biedt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account.   Gebruik indien mogelijk Azure AD-referenties als een beveiligings best practice, in plaats van de account sleutel te gebruiken, die eenvoudiger kan worden aangetast. Wanneer het ontwerp van uw toepassing gedeelde toegangs handtekeningen vereist voor toegang tot Blobopslag, moet u Azure AD-referenties gebruiken om een gebruikers delegering van Shared Access signatures (SAS) te maken wanneer dat mogelijk is voor een superieure beveiliging.

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources](authorization-resource-provider.md)

- [Toegang tot Azure-Blob en wachtrij gegevens configureren met Azure RBAC in Azure Portal](storage-auth-aad-rbac-portal.md)

- [Toegang tot gegevens in Azure Storage autoriseren](storage-auth.md)

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Raadpleeg de logboeken van de Azure Active Directory (Azure AD) om verouderde accounts te detecteren, die kunnen bestaan uit beheerders rollen voor het opslag account. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot de resources van het opslag account en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 
 

 
U kunt ook een Shared Access Signature (SAS) gebruiken om beveiligde gedelegeerde toegang tot resources in uw opslag account te bieden zonder de beveiliging van uw gegevens in gevaar te brengen. U kunt bepalen welke resources de client mag openen, welke machtigingen ze hebben op deze resources en hoe lang de SAS geldig is, onder andere para meters.
 

 
Lees ook anonieme lees toegang tot containers en blobs. De standaardinstelling is dat een container en alle bijbehorende blobs alleen toegankelijk zijn voor een gebruiker die over de juiste machtigingen beschikt. U kunt Azure Monitor gebruiken om te waarschuwen voor anonieme toegang voor opslag accounts met behulp van anonieme verificatie.
 

 
Een efficiënte manier om het risico van onvermoede gebruikers accounts te beperken, is het beperken van de duur van toegang die u verleent aan gebruikers. Time-Limited SAS-Uri's zijn een efficiënte manier om gebruikers toegang tot een opslag account automatisch te laten verlopen. Daarnaast is het draaien van opslag account sleutels regel matig een manier om ervoor te zorgen dat onverwachte toegang via de sleutels van het opslag account een beperkte duur is.
 

 
- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/) 
 

 
- [Toegang op het niveau van Azure Storage-account weer geven en wijzigen](storage-auth-aad-rbac-portal.md)
 

 
- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
 

 
- [Anonieme leestoegang tot containers en blobs beheren](../blobs/anonymous-read-access-configure.md)
 

 
- [Een Storage-account bewaken in de Azure-portal](manage-storage-analytics-logs.md)
 

 
- [Toegangs sleutels voor opslag accounts beheren](storage-account-keys-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: met Opslaganalyse kunt u gedetailleerde informatie over geslaagde en mislukte aanvragen in een opslag service vastleggen. Alle logboeken worden opgeslagen in blok-blobs in een container met de naam $logs, die automatisch worden gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag account.
 

Diagnostische instellingen maken voor gebruikers accounts voor Azure Active Directory (Azure AD), de audit logboeken en aanmeldings logboeken verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

Als u verificatie fouten wilt controleren op Azure Storage accounts, kunt u waarschuwingen maken om u te waarschuwen wanneer bepaalde drempel waarden zijn bereikt voor metrische gegevens van de opslag resource. Daarnaast kunt u met behulp van Azure Monitor op anonieme toegang waarschuwen voor opslag accounts met behulp van anonieme verificatie.

- [Azure Storage-analyselogboeken](storage-analytics-logging.md)
 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Metrische waarschuwingen voor Azure Storage accounts configureren](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) de functies risico-en identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op de resources van uw opslag account. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren.

- [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang moet krijgen tot klant gegevens, biedt klanten-lockbox (Preview voor het opslag account) een interface waarmee klanten aanvragen voor gegevens toegang van klanten kunnen controleren en goed keuren of afwijzen. Micro soft vereist geen toegang tot de geheimen van uw organisatie die zijn opgeslagen in het opslag account.

- [Klanten-lockbox begrijpen](../../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van opslag account bronnen voor het opslaan of verwerken van gevoelige informatie. 

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met behulp van afzonderlijke abonnementen, beheer groepen en opslag accounts voor afzonderlijke beveiligings domeinen, zoals omgeving en gegevens gevoeligheid.  U kunt uw opslag account beperken om het toegangs niveau te bepalen voor uw opslag accounts die uw toepassingen en bedrijfs omgevingen vereisen, op basis van het type en de subset van netwerken die worden gebruikt. Wanneer netwerk regels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot een opslag account. U kunt de toegang tot Azure Storage beheren via Azure RBAC (Azure RBAC).

U kunt ook privé-eind punten configureren om de beveiliging te verbeteren als verkeer tussen uw virtuele netwerk en de service gaat over het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

- [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)

- [Service-eindpunten voor virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor opslag account bronnen die gevoelige informatie opslaan of verwerken, markeert u de resources als gevoelig met behulp van tags. Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure Storage accounts met behulp van Azure Firewall.  

Daarnaast kunt u het virtuele netwerk service-eindpunt beleid gebruiken om uitgaand virtueel netwerk verkeer te filteren op Azure Storage accounts via service-eind punten en alleen gegevens exfiltration toe te staan op specifieke Azure Storage accounts.

- [Azure Storage-firewalls en virtuele netwerken configureren](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Service-eindpuntbeleid voor virtueel netwerk voor Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: u kunt het gebruik van HTTPS afdwingen door de vereiste beveiligde overdracht voor het opslag account in te scha kelen. Als u deze optie inschakelt, worden verbindingen via HTTP geweigerd.  Daarnaast kunt u Azure Security Center en Azure Policy gebruiken om een veilige overdracht af te dwingen voor uw opslag account.

- [Veilige overdracht vereisen in Azure Storage](storage-require-secure-transfer.md)

- [Azure-beveiligings beleid bewaakt door Security Center](../../security-center/policy-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: gegevens identificatie functies zijn nog niet beschikbaar voor Azure Storage account en gerelateerde resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden. 

- [Informatie over beveiliging van klantgegevens in Azure](../../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Hulp**: met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via toegangs beheer op basis van rollen (Azure RBAC). Azure Storage definieert een set ingebouwde RBAC-rollen van Azure die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot BLOB-of wachtrij gegevens.

- [Azure RBAC-rollen toewijzen voor Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources](authorization-resource-provider.md)

- [Toegang tot Azure-Blob en wachtrij gegevens configureren met Azure RBAC in Azure Portal](storage-auth-aad-rbac-portal.md)

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Toegang tot gegevens in Azure Storage autoriseren](storage-auth.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Storage versleuteling is ingeschakeld voor alle opslag accounts en kan niet worden uitgeschakeld. Azure Storage versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard. Wanneer u gegevens uit Azure Storage leest, worden deze door Azure Storage ontsleuteld voordat ze worden geretourneerd. Met Azure Storage versleuteling kunt u uw gegevens op rest beveiligen zonder dat u code hoeft te wijzigen of code aan toepassingen hoeft toe te voegen. 

- [Meer informatie over Azure Storage versleuteling in rust](storage-service-encryption.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in de resources van het opslag account.  U kunt ook Azure Storage logboek registratie inschakelen om bij te houden hoe elke aanvraag voor Azure Storage is geautoriseerd. De logboeken geven aan of een aanvraag anoniem is gemaakt door gebruik te maken van een OAuth 2,0-token, met behulp van gedeelde sleutel of door gebruik te maken van een Shared Access Signature (SAS).  Daarnaast kunt u met behulp van Azure Monitor op anonieme toegang waarschuwen voor opslag accounts met behulp van anonieme verificatie. 

 
- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage-analyselogboeken](storage-analytics-logging.md) 

 
- [Metrische waarschuwingen voor Azure Storage accounts configureren](manage-storage-analytics-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg aanbevelingen van Azure Security Center om de configuratie van uw opslag accounts continu te controleren en te controleren.  

- [Aanbevelingen voor beveiliging: een naslaggids](../../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor opslag accounts.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center. 

- [Azure Security Center beveiligde Score begrijpen](/azure/security-center/security-center-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (inclusief opslag accounts) in uw abonnement (en) te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

- [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op opslag account resources die meta gegevens geven om ze logisch in een taxonomie te organiseren. 

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om opslag accounts en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement. 

Gebruik ook Azure Defender voor opslag om niet-geautoriseerde Azure-resources te detecteren. 

- [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md) 

- [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources maken conform uw organisatie behoeften.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities: 

 
 - Niet toegestane resourcetypen 
 
 - Toegestane brontypen 
 

 
Daarnaast gebruikt u de resource grafiek van Azure om resources in de abonnementen op te vragen en te detecteren. Dit kan handig zijn in omgevingen met hoge beveiliging, zoals die met opslag accounts. 
 

 
- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: klanten kunnen voor komen dat resources worden gemaakt of gebruikt met Azure policy zoals vereist door het bedrijfs beleid van de klant. 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Ingebouwde definities Azure Policy-micro soft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, zoals die met opslag accounts, wordt voor komen. 

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte **micro soft. Storage** om aangepaste beleids regels te maken om de configuratie van uw opslag account-exemplaren te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities voor Azure Storage account gebruiken, zoals:

- Onbeperkte netwerktoegang tot opslagaccounts controleren
- Azure Defender voor opslag implementeren
- Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources
- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld

Gebruik aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw opslag accounts.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor de resources van uw opslag account. 

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md) 

- [Wat zijn Azure Policy effecten?](../../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen, gewenste status configuratie scripts, enzovoort. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Azure AD indien geïntegreerd met TFS.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: maakt gebruik van Azure Policy om systeem configuraties voor opslag accounts te signasen, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen. 

- [ Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: Maak gebruik van Azure Security Center om basislijn scans uit te voeren voor de resources van uw Azure Storage-account. 

- [Aanbevelingen herstellen in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. U kunt door micro soft beheerde sleutels gebruiken voor de versleuteling van het opslag account of versleuteling beheren met hun eigen sleutels. Als u door de klant verschafte sleutels gebruikt, kunt u gebruikmaken van Azure Key Vault om de sleutels veilig op te slaan. 

U kunt ook de sleutels van het opslag account regel matig draaien om de impact van het verlies of de openbaar making van de sleutels van het opslag account te beperken.

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)

- [Toegangs sleutels voor opslag accounts beheren](storage-account-keys-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: toegang verlenen tot blobs en wacht rijen binnen Azure Storage Accounts met Azure Active Directory (Azure AD) en beheerde identiteiten. Azure-Blob-en wachtrij opslag bieden ondersteuning voor Azure AD-verificatie met beheerde identiteiten voor Azure-resources. 

Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot Blob-en wachtrij gegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure Virtual Machines r, functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.

- [Toegang verlenen tot Azure Blob-en wachtrij gegevens met behulp van een beheerde identiteit](storage-auth-aad-rbac-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [ Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: Azure Defender voor opslag gebruiken voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actieve Tor-afsluit knooppunt (een anoniem-proxy). 
 

 
U kunt ook inhoud voor schadelijke software scannen voordat u deze uploadt naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage en anderen om te voldoen aan de vereisten van uw organisatie.
 

 
- [Azure Defender voor Storage configureren](azure-defender-storage-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: de gegevens in uw Microsoft Azure Storage-account worden altijd automatisch gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen. Azure Storage kopieert uw gegevens, zodat deze worden beveiligd tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. U kunt ervoor kiezen om uw gegevens te repliceren binnen hetzelfde Data Center, op zonegebonden-data centrums binnen dezelfde regio of in geografisch gescheiden regio's. 

U kunt Azure Automation ook inschakelen om regel matige moment opnamen van de blobs te maken.

- [Informatie over Azure Storage redundantie en Service-Level-overeenkomsten](storage-redundancy.md)

- [Een moment opname van een BLOB maken](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Overzicht van Azure Automation](../../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: om een back-up te maken van gegevens van services die worden ondersteund door het opslag account, zijn er meerdere methoden beschikbaar, met inbegrip van azcopy of hulpprogram ma's van derden. Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval.
 

Door de klant beheerd/verschafte sleutels kunnen worden ondersteund in Azure Key Vault met behulp van Azure CLI of Power shell. 

 
- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)  

- [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: regel matig gegevens herstel van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen uitvoeren met de volgende Power shell-opdrachten:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Key Vault certificaten herstellen](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Key Vault sleutels herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Key Vault beheerde opslag accounts herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault geheimen herstellen](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs, bestanden en tabel gegevens naar of van een opslag account](storage-use-azcopy-v10.md)

Opmerking: als u gegevens wilt kopiëren van en naar de Azure Table Storage-service, installeert u vervolgens AzCopy versie 7,3.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als u door de klant beheerde sleutels wilt inschakelen voor een opslag account, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen voorlopig verwijderen en niet wissen in de sleutel kluis inschakelen.  Met de functie voor voorlopig verwijderen van Key Vault kunt u verwijderde kluizen en kluis objecten, zoals sleutels, geheimen en certificaten, herstellen.  Als er een back-up wordt gemaakt van gegevens van een opslag account naar Azure Storage blobs, schakelt u de optie zacht verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd  Behandel uw back-ups als gevoelige gegevens en pas de relevante besturings elementen voor toegang en gegevens bescherming toe als onderdeel van deze basis lijn.  Daarnaast kunt u voor betere beveiliging essentiële gegevens objecten van het bedrijf opslaan in een WORM (Write Once, Read Many).

- [De tijdelijke verwijdering van Azure Key Vault gebruiken](../../key-vault/general/key-vault-recovery.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/soft-delete-blob-overview.md)

- [Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](../blobs/storage-blob-immutable-storage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Hand leiding voor het verwerken van beveiligings incidenten van het NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid. 

 
Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.
 

 
- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)
 

 
- [Tags gebruiken om Azure-resources te organiseren](../../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.
    

- [ Werk stroom automatisering en Logic Apps configureren](../../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
