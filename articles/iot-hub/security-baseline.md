---
title: Azure-beveiligingsbasislijn voor Azure IoT Hub
description: De Azure IoT Hub beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587626"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure-beveiligingsbasislijn voor Azure IoT Hub

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toe op Microsoft Azure IoT Hub. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure IoT Hub. **Besturingselementen** die niet van Azure IoT Hub zijn uitgesloten.

 
Als u wilt zien Azure IoT Hub volledig is toegewezen aan de Azure Security-benchmark, bekijkt u het volledige Azure IoT Hub het [toewijzingsbestand voor beveiligingsbasislijnen.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** IoT Hub een Platform-as-a-Service (PaaS) met meerdere tenants is, delen verschillende klanten dezelfde groep reken-, netwerk- en opslaghardwareresources. IoT Hub hostnamen van de IoT Hub worden via internet aan een openbaar eindpunt met een openbaar routeerbaar IP-adres toe te staan. Verschillende klanten delen dit IoT Hub openbaar eindpunt en IoT-apparaten in via wide-area netwerken en on-premises netwerken hebben allemaal toegang tot dit eindpunt. Microsoft heeft de service ontworpen voor volledige isolatie tussen de gegevens van elke tenant en werkt continu om dit resultaat te garanderen.

IoT Hub functies zoals berichtroutering, bestandsupload en bulksgewijs importeren/exporteren van apparaten vereisen ook connectiviteit van IoT Hub naar een Azure-resource die eigendom is van de klant via het openbare eindpunt. Deze verbindingspaden maken gezamenlijk het verkeer van de IoT Hub naar de resources van de klant.

U wordt aangeraden de connectiviteit met uw Azure-resources (inclusief Azure IoT Hub) te beperken via een virtueel netwerk waarvan u eigenaar bent en dat u gebruikt om de blootstelling aan connectiviteit in een geïsoleerd netwerk te beperken en on-premises netwerkconnectiviteit rechtstreeks met het Backbone-netwerk van Azure in te stellen. Gebruik Azure Private Link azure-privé-eindpunt, indien mogelijk, om privétoegang tot uw services vanuit andere virtuele netwerken mogelijk te maken. 

Zodra de privétoegang tot stand is gebracht, schakelt u openbare netwerktoegang voor de IoT Hub uit voor extra beveiliging. Dit besturingselement op netwerkniveau wordt afgedwongen op een specifieke IoT-hub-resource, waardoor isolatie wordt gewaarborgd. Om de service actief te houden voor andere klantresources met behulp van het openbare pad, blijft het openbare eindpunt op te lossen, blijven IP-adressen detecteerbaar en blijven poorten geopend. Dit is geen reden tot zorg omdat Microsoft meerdere beveiligingslagen integreert om volledige isolatie tussen tenants te garanderen.

Houd open hardwarepoorten op uw apparaten tot een minimum beperkt om ongewenste toegang te voorkomen. Daarnaast kunt u mechanismen bouwen om fysieke manipulatie van het apparaat te voorkomen of te detecteren.

- [Ondersteuning voor virtuele IoT-netwerken](virtual-network-support.md)

- [Openbare netwerktoegang beheren voor IoT Hub](iot-hub-public-network-access.md)

- [Isolatie van tenants in Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [LoT-netwerk best practice](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Azure Private Link overzicht](../private-link/private-link-overview.md)

- [Azure-netwerkbeveiligingsgroep](../virtual-network/network-security-groups-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's bewaken en in een logboek plaatsen

**Richtlijnen:** gebruik Azure Security Center en volg de aanbevelingen voor netwerkbeveiliging om uw Azure-netwerkbronnen te beveiligen. Schakel stroomlogboeken voor netwerkbeveiligingsgroep in en verzend de logboeken naar een Azure Storage voor controle. U kunt de stroomlogboeken ook verzenden naar een Log Analytics-werkruimte en vervolgens Traffic Analytics om inzicht te krijgen in verkeerspatronen in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren, hotspots en beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.
 
- [Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)
 
- [Informatie over netwerkbeveiliging die wordt geboden door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid:** Niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Bekende schadelijke IP-adressen blokkeren met IoT Hub IP-filterregels. Schadelijke pogingen worden ook vastgelegd en gewaarschuwd via Azure Security Center voor IoT.

Azure DDoS Protection Basic is al ingeschakeld en beschikbaar zonder extra kosten als onderdeel van IoT Hub. Bewaking van altijd aan-verkeer en realtime beperking van veelvoorkomende aanvallen op netwerkniveau bieden dezelfde verdedigingslinie die wordt gebruikt door de microsoft-onlineservices. De volledige schaal van het wereldwijde netwerk van Azure kan worden gebruikt om aanvalsverkeer tussen regio's te distribueren en te beperken.

- [IoT Hub IP-filter](iot-hub-ip-filtering.md)

- [Azure Security Center voor communicatie met verdachte IP-adressen van IoT](/azure/asc-for-iot/concept-security-alerts)

- [Azure DDoS Protection Basic beheren](../ddos-protection/ddos-protection-overview.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor aanbiedingen die netwerkpakketten produceren die kunnen worden vastgelegd en bekeken door klanten. IoT Hub produceert geen netwerkpakketten die klantgericht zijn en is niet ontworpen om rechtstreeks in virtuele Azure-netwerken te implementeren.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** selecteer een aanbieding in Azure Marketplace ondersteuning biedt voor id's/IPS-functionaliteit met mogelijkheden voor nettoladinginspectie.  Wanneer payloadinspectie geen vereiste is, kunnen Azure Firewall bedreigingsinformatie worden gebruikt. Azure Firewall filteren op basis van bedreigingsinformatie wordt gebruikt om verkeer van en naar bekende schadelijke IP-adressen en domeinen te waarschuwen of te blokkeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blokkeren. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** voor resources die toegang tot uw Azure IoT Hub nodig hebben, gebruikt u Virtual Network-servicetags om besturingselementen voor netwerktoegang te definiëren voor netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld AzureIoTHub) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres-voorvoegsels die door de servicetag worden omvat en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

- [Servicetags gebruiken voor Azure IoT](iot-hub-understand-ip-address.md)
- [Voor meer informatie over het gebruik van servicetags](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die zijn gekoppeld aan uw Azure IoT Hub-naamruimten met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten Microsoft.Devices en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Machine Learning te controleren of af te dwingen. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** gebruik tags voor netwerkbronnen die zijn gekoppeld aan Azure IoT Hub implementatie om deze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren voor netwerkresources met betrekking tot Azure IoT Hub. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen via Azure Monitor voor het samenvoegen van beveiligingsgegevens die zijn gegenereerd door Azure IoT Hub. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren, en gebruik opslagaccounts voor langetermijn-/archiveringsopslag. U kunt ook gegevens inschakelen en inschakelen voor Azure Sentinel of een SIEM (Security Incident and Event Management) van derden.

- [Azure IoT-logboeken instellen](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Diagnostische azure IoT-instellingen inschakelen voor Azure-resources voor toegang tot audit-, beveiligings- en resourcelogboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, doeladressen en andere nuttige elementen.

- [Logboeken Azure IoT Hub instellen](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Logboekregistratie en verschillende logboektypen in Azure begrijpen](../azure-monitor/essentials/platform-logs-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Devices:**

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor logboekretentieperiode in voor Log Analytics-werkruimten die zijn gekoppeld aan uw Azure IoT Hub-instanties overeenkomstig de nalevingsvoorschriften van uw organisatie.

- [Retentieparameters voor logboeken instellen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer logboeken op afwijkende gedrag en controleer regelmatig de resultaten van uw Azure IoT Hub. Gebruik Azure Monitor en een Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens.

U kunt ook gegevens inschakelen en inschakelen voor Azure Sentinel siem van derden. 

- [Azure IoT-status bewaken](monitor-iot-hub.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Aan de slag met Log Analytics-query's](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Security Center voor IoT met een Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en -gebeurtenissen. U kunt ook gegevens inschakelen en inschakelen om gegevens te Azure Sentinel. U kunt ook operationele waarschuwingen definiëren met Azure Monitor die mogelijk gevolgen hebben voor de beveiliging, bijvoorbeeld wanneer het verkeer onverwacht afvalt.

- [Status van Azure IoT Hub bewaken](monitor-iot-hub.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Azure Security Center voor IoT-waarschuwingen](/azure/asc-for-iot/concept-security-alerts)

- [Waarschuwingen voor logboekgegevens van Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** Niet van toepassing; Azure IoT Hub verwerkt of produceert geen antimalwarelogboeken.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Niet van toepassing; Azure IoT Hub verwerkt of produceert geen DNS-gerelateerde logboeken.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Controlelogregistratie via opdrachtregel inschakelen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u de toegang tot Azure IoT Hub beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, service-principals voor groepen en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogramma's zoals Azure CLI of Azure PowerShell of de Azure Portal.

- [Een directoryrol krijgen in Azure Active Directory (Azure AD) met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Toegangsbeheer Azure IoT Hub resources wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaardwachtwoorden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken.

U kunt ook Just-In-Time-toegang tot beheerdersaccounts inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management en Azure Resource Manager.

- [Meer informatie over Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Eenmalige aanmelding (SSO) gebruiken met Azure Active Directory

**Richtlijnen:** voor gebruikers die toegang hebben IoT Hub, gebruikt Azure Active Directory (Azure AD) SSO. Gebruik Azure Security Center identiteits- en toegangsaanbevelingen.

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen om uw algehele Azure-tenant te beveiligen, zodat alle services profiteren. IoT Hub-service biedt geen ondersteuning voor meervoudige verificatie.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Gebruik een PAW (Secure Privileged Access Workstation) voor beheertaken waarvoor verhoogde bevoegdheden zijn vereist.

- [Inzicht in beveiligde werkstations met bevoegde toegang](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie Azure Active Directory (Azure AD) inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten en -bewaking om te detecteren wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** voor gebruikers die toegang IoT Hub, wordt voorwaardelijke toegang niet ondersteund. Als u dit wilt beperken, gebruikt u Azure Active Directory (Azure AD) benoemde locaties om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's voor uw algehele Azure-tenant, met alle services, inclusief IoT Hub.

- [Benoemde locaties in Azure AD configureren](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Voor gebruikerstoegang tot IoT Hub gebruikt u Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

Voor toegang tot apparaten en services gebruikt IoT Hub beveiligingstokens en SHARED ACCESS SIGNATURE-tokens (SAS) om apparaten en services te verifiëren om te voorkomen dat sleutels in het netwerk worden verzonden. 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub beveiligingstokens](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Gebruik daarnaast Azure AD-identiteits- en toegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Gebruik Azure AD Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Een Azure AD Privileged Identity Management (PIM) implementeren](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteit, -controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met elk SIEM-/bewakingshulpprogramma.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt gewenste waarschuwingen configureren in de Log Analytics-werkruimte.

Gebruikers Azure Monitor resourcelogboeken voor het bewaken van niet-geautoriseerde verbindingspogingen in de categorie Verbindingen.

- [Azure-activiteitenlogboeken integreren met Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Resourcelogboeken configureren voor IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection-functies om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [ Riskante aanmeldingen van Azure AD weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [ Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang moet hebben tot klantgegevens, wordt dit rechtstreeks aangevraagd bij de klant.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.
 
- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Isolatie implementeren met behulp van afzonderlijke abonnementen en beheergroepen voor afzonderlijke beveiligingsdomeinen, zoals omgevingstype en gevoeligheidsniveau voor gegevens. U kunt het toegangsniveau beperken tot uw Azure-resources die uw toepassingen en bedrijfsomgevingen nodig hebben. U kunt de toegang tot Azure-resources beheren via Azure RBAC.
  
- [ Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [ Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [ Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** Gebruik een oplossing van derden Azure Marketplace in netwerkperimeters om te controleren op onbevoegde overdracht van gevoelige informatie en om dergelijke overdrachten te blokkeren tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en beschermt tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een reeks krachtige besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** IoT Hub gebruikt Transport Layer Security (TLS) om verbindingen van IoT-apparaten en -services te beveiligen. Er worden momenteel drie versies van het TLS-protocol ondersteund, namelijk versies 1.0, 1.1 en 1.2. Het wordt ten zeerste aangeraden TLS 1.2 als TLS-versie van de voorkeur te gebruiken wanneer u verbinding maakt met IoT Hub.

Volg Azure Security Center aanbevelingen voor versleuteling in rust en versleuteling tijdens overdracht, indien van toepassing.

- [TLS-ondersteuning in IoT Hub](iot-hub-tls-support.md)
- [Inzicht in versleuteling tijdens overdracht met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** functies voor gegevensidentificatie, classificatie en preventie van verlies zijn nog niet beschikbaar voor Azure IoT Hub. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende Azure-platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het veel te ver om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een reeks krachtige besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om toegang tot resources te beheren

**Richtlijnen:** gebruik Azure RBAC om toegang te IoT Hub gebruikerstoegang tot de besturingsvlak. Gebruik voor toegang tot gegevensvlak IoT Hub beleid voor gedeelde toegang voor IoT Hub.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer wijzigingen plaatsvinden in productie-exemplaren van Azure IoT Hub en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een geautomatiseerde oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een proces voor risicoclassificatie gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources (niet alle resources ondersteunen tags, maar de meeste wel) om ze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.
  
- [ Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op de behoeften van uw organisatie.

Elke IoT Hub heeft een identiteitsregister dat kan worden gebruikt voor het maken van resources per apparaat in de service. Afzonderlijke of groepen apparaat-id's kunnen worden toegevoegd aan een allowlist of een blokkeringslijst, waardoor volledige controle over de toegang tot apparaten mogelijk is.

- [IoT Hub identiteitsregister](iot-hub-devguide-identity-registry.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt. 

Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren.  Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

* Niet toegestane resourcetypen
* Toegestane brontypen

Gebruik daarnaast de Azure Resource Graph om resources in de abonnementen op te vragen/te ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** Niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [ Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren in rekenbronnen beperken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid:** Niet van toepassing

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Iot Hub-service met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.Devices om aangepaste beleidsregels te maken om de configuratie van uw Azure IoT Hub afdwingen.

Azure Resource Manager heeft de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie.

U kunt ook de aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige besturingssysteemconfiguraties maken

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources. Daarnaast kunt u de sjablonen Azure Resource Manager om de beveiligingsconfiguratie te onderhouden van uw Azure-resources die uw organisatie nodig heeft.  
 
- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt voor uw Azure IoT Hub of gerelateerde resources, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste besturingssysteeminstallatie

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy-aliassen in de naamruimte Microsoft.Devices om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** Niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure-resources. Gebruik daarnaast Azure Policy om Azure-resourceconfiguraties te waarschuwen en te controleren. 
 
- [ Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor rekenbronnen.

**Verantwoordelijkheid:** niet van toepassing

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** IoT Hub beveiligingstokens en sas Shared Access Signature tokens (SAS) gebruiken om apparaten en services te verifiëren om te voorkomen dat sleutels in het netwerk worden verzonden. 

Gebruik beheerde identiteiten in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen.

- [IoT Hub beveiligingstokens](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Beheerde identiteiten gebruiken voor IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Een sleutelkluis maken](../key-vault/general/quick-create-portal.md)

- [Verificatie met Key Vault met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** IoT Hub beveiligingstokens en sas Shared Access Signature tokens gebruiken om apparaten en services te verifiëren om te voorkomen dat sleutels in het netwerk worden verzonden.

Gebruik beheerde identiteiten om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code.

- [IoT Hub beveiligingstokens](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Beheerde identiteiten configureren voor IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 
 
- [  Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Microsoft Anti-malware is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure IoT Hub), maar wordt niet uitgevoerd op klantinhoud.

Het is uw verantwoordelijkheid om alle inhoud die wordt geüpload naar niet-compute Azure-resources vooraf te scannen. Microsoft heeft geen toegang tot klantgegevens en kan daarom namens u geen antimalwarescans van klantinhoud uitvoeren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Azure IoT Hub-service biedt methoden en frameworks om uw IoT Hub-services zeer beschikbaar en herstelbaar te maken na noodscenario's op basis van specifieke bedrijfsdoelstellingen. 

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](iot-hub-ha-dr.md)

- [Een kloon van IoT Hub](iot-hub-how-to-clone.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Azure IoT Hub dat de secundaire IoT-hub alle apparaat-id's moet bevatten die verbinding kunnen maken met de oplossing. De oplossing moet geo-gerepliceerde back-ups van apparaat-id's behouden en deze uploaden naar de secundaire IoT-hub voordat u het actieve eindpunt voor de apparaten overschakelt. De exportfunctionaliteit voor apparaat-IoT Hub is nuttig in deze context.

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub apparaat-id exporteren](iot-hub-bulk-identity-mgmt.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Azure IoT Hub dat de secundaire IoT-hub alle apparaat-id's moet bevatten die verbinding kunnen maken met de oplossing. De oplossing moet geo-gerepliceerde back-ups van apparaat-id's behouden en deze uploaden naar de secundaire IoT-hub voordat u het actieve eindpunt voor de apparaten overschakelt. De exportfunctionaliteit voor apparaat-IoT Hub is nuttig in deze context.

Periodiek gegevensherstel van inhoud in back-up uitvoeren. Zorg ervoor dat u back-up van door de klant beheerde sleutels kunt herstellen.

- [Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub apparaat-id exporteren](iot-hub-bulk-identity-mgmt.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Schakel de beveiliging voor het verwijderen van gegevens in Key Vault om sleutels te beveiligen tegen onbedoelde of kwaadwillende verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van back-ups, kunt u de functie voor het opslaan en herstellen van uw gegevens inschakelen wanneer blobs of blob-momentopnamen worden verwijderd.

 
- [Inzicht in Azure RBAC](../role-based-access-control/overview.md)

- [Zacht verwijderen voor Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Ontwikkel een handleiding voor het reageren op incidenten voor uw organisatie. Zorg ervoor dat er plannen voor het reageren op incidenten zijn die alle rollen van personeel definiëren, evenals de fasen van incidentafhandeling en -beheer, van detectie tot incidentbeoordeling. 
  
- [ Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Gebruik de computerbeveiligingsincidentafhandelingshandleiding van NIST om u te helpen bij het maken van uw eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Azure Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het zoeken of de analytische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit zit die tot de waarschuwing heeft geleid.

  
 Daarnaast kunt u abonnementen markeren met behulp van tags en een naamgevingssysteem maken om Azure-resources te identificeren en categoriseren, met name de resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de kritiekheid van de Azure-resources en -omgeving waar het incident zich heeft voorgedaan.
  
- [ Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Identificeer zwakke punten en hiaten en pas vervolgens uw reactieplan naar behoefte aan.
  
- [ Publicatie van NIST- Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.
  
- [ De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie voor continue export om risico's voor Azure-resources te identificeren. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.
  
- [ Continue export configureren](../security-center/continuous-export.md)
 
- [ Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** Gebruik de functie voor Azure Security Center automatisch reacties op beveiligingswaarschuwingen en aanbevelingen te activeren om uw Azure-resources te beveiligen.
  
- [ Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

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
