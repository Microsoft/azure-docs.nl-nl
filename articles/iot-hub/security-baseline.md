---
title: Azure-beveiligings basislijn voor Azure IoT Hub
description: De beveiligings basislijn van Azure IoT Hub biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 003bf5241611df450fa6d0ecadff264c4d5de243
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842852"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure-beveiligings basislijn voor Azure IoT Hub

De Azure-beveiligings basislijn voor Microsoft Azure IoT Hub bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren. De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices. Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: standaard worden de hostnamen van IOT hub toegewezen aan een openbaar eind punt met een openbaar routeerbaar IP-adres via internet. Verschillende klanten delen dit IoT Hub open bare eind punt en IoT-apparaten in meer dan Wide Area Networks en on-premises netwerken hebben toegang tot het netwerk.

IoT Hub functies, waaronder bericht routering, het uploaden van bestanden en bulksgewijs importeren/exporteren van apparaten, moet u ook een verbinding van IoT Hub hebben met een Azure-resource die eigendom is van een klant over het open bare eind punt. Deze connectiviteits paden vormen samen het uitgaande verkeer van IoT Hub naar klant resources.

Het is raadzaam om de verbinding met uw Azure-resources (inclusief Azure IoT Hub) te beperken via een virtueel netwerk waarvan u de verbindings Risico's in een geïsoleerd netwerk wilt verminderen en on-premises netwerk connectiviteit rechtstreeks wilt inschakelen voor het Azure-backbone-netwerk. Gebruik een persoonlijke Azure-koppeling en een persoonlijk Azure-eind punt, waar mogelijk, om persoonlijke toegang tot uw services in te scha kelen vanuit andere virtuele netwerken.

Open de poorten op uw apparaten zo min tot Maxi maal om ongewenste toegang te voor komen. Daarnaast bouwt u mechanismen op om het apparaat te voor komen of fysiek te knoeien.

- [Ondersteuning voor IoT Virtual Networks](virtual-network-support.md)
- [best practice voor partij netwerken](../iot-fundamentals/security-recommendations.md?context=azure%252fiot-hub%252frc%252frc#networking)
- [Overzicht van persoonlijke Azure-koppelingen](../private-link/private-link-overview.md)
- [Azure-netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg de aanbevelingen voor netwerk beveiliging om uw Azure-netwerk bronnen te beveiligen. Schakel logboeken stroom van de netwerk beveiligings groep in en verzend de logboeken naar een Azure Storage account voor controle. U kunt de stroom logboeken ook naar een Log Analytics-werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.
 
- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)
 
- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: blok keer bekende schadelijke IP-adressen met IOT hub IP-filter regels. Er worden ook kwaad aardige pogingen vastgelegd en gewaarschuwd via Azure Security Center voor IoT.

Azure DDoS Protection Basic is al ingeschakeld en beschikbaar voor geen extra kosten als onderdeel van IoT Hub. Controle over altijd verkeer en real-time beperking van veelvoorkomende aanvallen op netwerk niveau, bieden dezelfde verdedigings die worden gebruikt door de onlineservices van micro soft. De volledige schaal van het wereld wijde netwerk van Azure kan worden gebruikt voor het distribueren en beperken van het aanvals verkeer tussen regio's.

- [IP-filter IoT Hub](iot-hub-ip-filtering.md)

- [Azure Security Center voor IoT verdachte IP-adres communicatie](../defender-for-iot/concept-security-alerts.md)

- [Azure DDoS Protection Basic beheren](../virtual-network/ddos-protection-overview.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor aanbiedingen die netwerk pakketten produceren die door klanten kunnen worden vastgelegd en weer gegeven. IoT Hub produceert geen netwerk pakketten die zijn gericht op de klant en is niet ontworpen om rechtstreeks in azure Virtual Networks te implementeren.

**Azure Security Center bewaking**: Nee

**Verantwoordelijkheid**: niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Selecteer een aanbieding van Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden.  Als Payload-inspectie geen vereiste is, kan Azure Firewall Threat Intelligence worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren wordt gebruikt om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blok keren. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor resources die toegang nodig hebben tot uw Azure-IOT hub, gebruikt u Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureIoTHub) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service tags gebruiken voor Azure IoT](iot-hub-understand-ip-address.md)
- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk resources die zijn gekoppeld aan uw Azure IOT hub-naam ruimten met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. devices ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Machine Learning-naam ruimten te controleren of af te dwingen. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Azure IOT hub-implementatie om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op Azure IOT hub. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure IOT hub voor tijds tempels in de logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure IOT hub. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik opslag accounts voor lange termijn/archiverings opslag. U kunt ook gegevens in-en inschakelen voor Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM).

- [Azure IoT-logboeken instellen](monitor-iot-hub-reference.md#resource-logs)
- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel de diagnostische instellingen van Azure IOT op Azure-resources in voor toegang tot de logboeken voor controle, beveiliging en bron. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

- [Azure IoT Hub-logboeken instellen](monitor-iot-hub-reference.md#resource-logs)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure IOT hub-instanties volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Analyseer en Controleer Logboeken op afwijkend gedrag en controleer regel matig de resultaten van uw Azure-IOT hub. Gebruik Azure Monitor en een Log Analytics werk ruimte om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure IoT-status controleren](monitor-iot-hub.md)
- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)
  
- [Aan de slag met Log Analytics query's](../azure-monitor/log-query/get-started-portal.md)
   
- [ Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center voor IOT met een log Analytics-werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden. U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten. U kunt ook operationele waarschuwingen definiëren met Azure Monitor die mogelijk gevolgen hebben voor de beveiliging, bijvoorbeeld wanneer het verkeer onverwacht daalt.

- [Status van Azure IoT Hub controleren](monitor-iot-hub.md)
- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)
- [Azure Security Center voor IoT-waarschuwingen](../defender-for-iot/concept-security-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure IoT Hub verwerkt of produceert geen anti-malware-gerelateerde Logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure IoT Hub verwerkt of produceert geen aan DNS gerelateerde Logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure IOT hub beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, of Azure PowerShell, of de Azure Portal. 

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: toegangs beheer naar Azure IOT hub resources worden beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaard wachtwoorden.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts.

U kunt ook Just-in-time-toegang tot beheerders accounts inschakelen met behulp van Azure AD Privileged Identity Management en Azure Resource Manager.

- [Meer informatie over Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory SSO voor gebruikers die toegang hebben tot IOT hub. Gebruik Azure Security Center-aanbevelingen voor identiteit en toegang. 

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van Azure AD MFA voor het beveiligen van uw algehele Azure-Tenant en voor delen van alle services. IoT Hub service heeft geen ondersteuning voor MFA.  

- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een beveiligd, door Azure beheerd werk station (ook wel een privileged Access Workstation of paw) voor beheer taken waarvoor verhoogde bevoegdheden zijn vereist.

- [Meer informatie over veilige, door Azure beheerde werk stations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory beveiligings rapporten en-bewaking om te detecteren wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)
- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Hulp**: voor gebruikers die toegang hebben tot IOT hub, wordt voorwaardelijke toegang niet ondersteund. Om dit te verhelpen, kunt u Azure AD benoemde locaties gebruiken om alleen toegang toe te staan vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's voor uw hele Azure-Tenant, profiteert alle services, waaronder IoT Hub. 

- [De benoemde locaties van Azure AD configureren](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor gebruikers toegang tot IOT hub. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Voor toegang via het apparaat en de service gebruikt IoT Hub beveiligings tokens en Shared Access Signature SAS-tokens om apparaten en services te verifiëren om te voor komen dat sleutels in het netwerk worden verzonden. 

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub beveiligings tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure AD Identity and Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Gebruik Azure AD Privileged Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)
- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)
- [Azure AD Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot de Azure AD-aanmeldings activiteit, audit en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma. 

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte. 

Gebruikers Azure Monitor bron logboeken voor het bewaken van niet-geautoriseerde Verbindings pogingen in de categorie verbindingen.

- [Azure-activiteiten logboeken integreren met Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Resource logboeken configureren voor IoT hub](monitor-iot-hub.md#collection-and-routing)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure AD Identity Protection functies om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.
  
- [ Risk ante aanmeldingen voor Azure AD weer geven](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang heeft tot klant gegevens, wordt deze rechtstreeks aangevraagd bij de klant. 

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbescherming

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.
 
- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure-resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure-resources beheren via Azure RBAC.
  
- [ Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)
- [ Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)
- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: gebruik een oplossing van een derde partij van Azure Marketplace in netwerk verbindingen om te controleren op niet-geautoriseerde overdracht van gevoelige informatie en om dergelijke overdrachten te blok keren terwijl u waarschuwt voor informatie beveiliging.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: IoT Hub gebruikt Transport Layer Security (TLS) om verbindingen van IOT-apparaten en-services te beveiligen. Er worden op dit moment drie versies van het TLS-protocol ondersteund, namelijk versie 1,0, 1,1 en 1,2. Het wordt ten zeerste aangeraden TLS 1,2 te gebruiken als de voor Keurs-TLS-versie wanneer u verbinding maakt met IoT Hub.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

- [TLS-ondersteuning in IoT Hub](iot-hub-tls-support.md)
- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure IOT hub. Implementeer een oplossing van derden als dat nodig is voor nalevings doeleinden.

Voor het onderliggende Azure-platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat het om tot een uitstekende lengte van het verlies en de bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: voor de controle van de gebruikers toegang tot IOT hub, gebruikt u Azure RBAC om de toegang te beheren. Gebruik het gedeelde toegangs beleid voor IoT Hub om toegang tot het gegevens vlak te IoT Hub.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Azure IoT Hub en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: IOT hub ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als ' uw eigen sleutel ' (BYOK).

Azure IoT Hub zorgt voor versleuteling van gegevens in rust en in transit omdat deze in onze data centers zijn geschreven en ontsleutelt voor u wanneer u er toegang toe hebt. IoT Hub maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens in rust te versleutelen.

- [Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub](iot-hub-customer-managed-keys.md)

- [Meer informatie over versleuteling in de rest van Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure IOT hub en andere essentiële of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure IoT Hub.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; Micro soft voert patch beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure IoT Hub. 

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources (niet alle resources ondersteunen Tags, maar de meeste taken) om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.
  
- [ Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)
  
- [ Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)
  
- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources conform uw organisatie behoeften.

Elk IoT Hub heeft een identiteits register dat kan worden gebruikt voor het maken van resources per apparaat in de service. Individuele identiteiten of groepen met apparaat-id's kunnen worden toegevoegd aan een lijst met toegestane apparaten of aan een blokkerings lijst, waardoor de toegang tot het apparaat volledig kan worden beheerd.

- [IoT Hub id-REGI ster](iot-hub-devguide-identity-registry.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen. 

Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren.  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Daarnaast kunt u met de Azure-resource grafiek bronnen in de abonnementen opvragen/ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure AD om de interactie van gebruikers met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.
  
- [ Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure IOT hub-service definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. devices ' om aangepaste beleids regels te maken om de configuratie van uw Azure IoT Hub-services te controleren of af te dwingen.

Azure Resource Manager kunt de sjabloon in JavaScript Object Notation (JSON) exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

U kunt ook de aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources. Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft.  
 
- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure IOT hub of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. devices ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans voor uw Azure-resources uit te voeren. Gebruik Azure Policy bovendien om een waarschuwing te krijgen en Azure-resource configuraties te controleren. 
 
- [ Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: IOT hub gebruikt beveiligings tokens en Shared Access Signature SAS-tokens om apparaten en services te verifiëren om te voor komen dat sleutels in het netwerk worden verzonden. 

Gebruik beheerde identiteiten in combi natie met Azure Key Vault om het geheime beheer van uw Cloud toepassingen te vereenvoudigen.

- [IoT Hub beveiligings tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Beheerde identiteiten voor IoT Hub gebruiken](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Een sleutel kluis maken](../key-vault/general/quick-create-portal.md)
- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: IOT hub gebruikt beveiligings tokens en Shared Access Signature SAS-tokens om apparaten en services te verifiëren om te voor komen dat sleutels in het netwerk worden verzonden. 

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder dat u referenties in uw code hoeft op te geven.

- [IoT Hub beveiligings tokens](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Beheerde identiteiten voor IoT Hub configureren](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 
 
- [  Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: centraal beheerde antimalware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure IOT hub), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: controleren of antimalware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze bench Mark is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: Azure IOT hub service biedt methoden en Framework om uw IOT hub-Services Maxi maal beschikbaar te maken en te herstellen op basis van nood gevallen, gebaseerd op specifieke zakelijke doel stellingen. 

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](iot-hub-ha-dr.md)

- [IoT Hub klonen](iot-hub-how-to-clone.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: Azure IOT hub raadt aan dat de secundaire IOT-hub alle apparaat-id's moet bevatten die verbinding kunnen maken met de oplossing. In de oplossing moeten geo-gerepliceerde back-ups van apparaat-id's worden bewaard en geüpload naar de secundaire IoT-hub voordat het actieve eind punt voor de apparaten wordt overgeschakeld. De functie voor het exporteren van de apparaat-id van IoT Hub is nuttig in deze context.

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exporteren van IoT Hub apparaat-id](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Azure IOT hub raadt aan dat de secundaire IOT-hub alle apparaat-id's moet bevatten die verbinding kunnen maken met de oplossing. In de oplossing moeten geo-gerepliceerde back-ups van apparaat-id's worden bewaard en geüpload naar de secundaire IoT-hub voordat het actieve eind punt voor de apparaten wordt overgeschakeld. De functie voor het exporteren van de apparaat-id van IoT Hub is nuttig in deze context.

Periodiek gegevens herstel van inhoud in back-up uitvoeren. Zorg ervoor dat u een back-up van door de klant beheerde sleutels kunt herstellen.

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exporteren van IoT Hub apparaat-id](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel de beveiliging van zacht verwijderen en leegmaken in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van back-ups, schakelt u de optie voor het tijdelijk verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd.
 
 
- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)
- [Zacht verwijderen voor Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident. 
  
- [ Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke intentie is achter de activiteit die tot de waarschuwing heeft geleid.

  
 Markeer bovendien abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.
  
- [ Beveiligings waarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig vervolgens uw reactie schema naar wens.
  
- [ Publicatie van het NIST-hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.
  
- [ De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.
  
- [ Continue export configureren](../security-center/continuous-export.md)
 
- [ Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: werk stroom automatisering gebruiken Azure Security Center om automatisch reacties te activeren op beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.
  
- [ Werk stroom automatisering configureren in Security Center](../security-center/workflow-automation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)