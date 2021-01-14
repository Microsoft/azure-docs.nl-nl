---
title: Azure-beveiligings basislijn voor Azure Stack Edge
description: De beveiligings basislijn van Azure Stack Edge biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0de08c166aba7609210892a32836bee9b07d5394
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203045"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azure-beveiligings basislijn voor Azure Stack Edge

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Microsoft Azure stack Edge. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure stack Edge. **Besturings elementen** die niet van toepassing zijn op Azure stack Edge, zijn uitgesloten.

Zie het [volledige Azure stack Edge-toewijzings bestand voor beveiligings basislijns](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)om te zien hoe Azure stack rand volledig is toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Richt lijnen**: klanten implementeren een door micro soft verschafte, fysieke Azure stack edge-apparaat in hun particuliere netwerk voor interne toegang en hebben opties om het te beveiligen. Het Azure Stack edge-apparaat is bijvoorbeeld toegankelijk via het interne netwerk van de klant en vereist een door de klant geconfigureerd IP-adres. Daarnaast wordt een toegangs wachtwoord gekozen door de klant om toegang te krijgen tot de gebruikers interface van het apparaat. 

Intern verkeer wordt verder beveiligd door:

- Transport Layer Security (TLS) versie 1,2 is vereist voor Azure Portal en SDK-beheer van het Azure Stack edge-apparaat.

- Elke client toegang tot het apparaat bevindt zich via de lokale web-UI met behulp van standaard TLS 1,2 als het standaard-beveiligde protocol.

- Alleen een geautoriseerd Azure Stack Edge Pro-apparaat mag lid worden van de Azure Stack Edge-service die de klant in hun Azure-abonnement heeft gemaakt.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.
 
- [TLS 1,2 configureren op Windows-clients die toegang krijgen tot Azure Stack Edge Pro GPU-apparaat](azure-stack-edge-j-series-configure-tls-settings.md)

- [Snelstartgids: aan de slag met Azure Stack Edge Pro met GPU](azure-stack-edge-gpu-quickstart.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Hulp**: klanten kunnen een punt-naar-site-VPN (virtueel particulier netwerk) kiezen om een Azure stack edge-apparaat te verbinden met het on-premises particuliere netwerk met het Azure-netwerk. VPN biedt een tweede laag code ring voor de gegevens in beweging via trans port Layer Security van het apparaat van de klant naar Azure. 

Klanten kunnen via de Azure Portal of via de Azure PowerShell een virtueel particulier netwerk configureren op hun Azure Stack edge-apparaat.

- [Azure VPN configureren via Azure PowerShell script voor Azure Stack Edge Pro R en Azure Stack Edge mini-R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [TLS 1,2 configureren op Windows-clients die toegang krijgen tot Azure Stack Edge Pro GPU-apparaat](azure-stack-edge-j-series-configure-tls-settings.md)

- [Zelfstudie: Certificaten voor uw Azure Stack Edge Pro R configureren](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Toegang tot Azure-services via particulier netwerk tot stand brengen

**Hulp**: klanten kunnen een punt-naar-site-VPN (virtueel particulier netwerk) kiezen om een Azure stack edge-apparaat te verbinden met het on-premises particuliere netwerk met het Azure-netwerk. VPN biedt een tweede laag code ring voor de gegevens in beweging via trans port Layer Security van het apparaat van de klant naar Azure. 

- [Azure VPN configureren via Azure PowerShell script voor Azure Stack Edge Pro R en Azure Stack Edge mini-R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [TLS 1,2 configureren op Windows-clients die toegang krijgen tot Azure Stack Edge Pro GPU-apparaat](azure-stack-edge-j-series-configure-tls-settings.md)

- [Zelfstudie: Certificaten voor uw Azure Stack Edge Pro R configureren](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Hulp**: het Azure stack edge-apparaat bevat standaard Windows Server-functies voor netwerk beveiliging, die niet door klanten kunnen worden geconfigureerd.

Klanten kunnen ervoor kiezen om hun particuliere netwerk dat is verbonden met Azure Stack edge-apparaat te beveiligen tegen externe aanvallen met een virtueel netwerk apparaat, zoals een firewall met geavanceerde DDoS-beveiliging (Distributed Denial of service).

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: de eind punten die worden gebruikt door Azure stack edge-apparaat, worden allemaal beheerd door micro soft. Klanten zijn verantwoordelijk voor alle extra besturings elementen die ze willen implementeren op hun on-premises systemen.

Het Azure Stack edge-apparaat maakt gebruik van de eigen inbraak detectie functies om de service te beveiligen. 

- [Azure Stack Edge-beveiliging begrijpen](azure-stack-edge-security.md)

- [Poort gegevens voor Azure Stack rand](azure-stack-edge-gpu-system-requirements.md)

- [Logboeken voor indringing van hardware en software-detectie ophalen](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management) voor meer informatie.*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Hulp**: alle Azure stack edge-apparaten hebben automatisch een door het systeem toegewezen beheerde identiteit in azure Active Directory (Azure AD). Op dit moment wordt de beheerde identiteit gebruikt voor het Cloud beheer van virtuele machines die worden gehost op Azure Stack Edge.

Azure Stack edge-apparaten worden opgestart in een vergrendelde status voor lokale toegang. Voor het lokale beheerders account moet u verbinding maken met uw apparaat via de lokale web-gebruikers interface of de Power shell-interface en een sterk wacht woord instellen. Sla de beheerders referenties van uw apparaat op een veilige locatie op, zoals een Azure Key Vault en roteer het beheerders wachtwoord op basis van de standaarden van uw organisatie.

- [Azure Stack edge-apparaat beveiligen via wacht woord](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: eenmalige aanmelding wordt niet ondersteund voor apparaten met Azure stack Edge-eind punten. U kunt er echter voor kiezen om standaard Azure Active Directory (Azure AD) op basis van eenmalige aanmelding in te scha kelen om de toegang tot uw Azure-Cloud bronnen te beveiligen.

- [Meer informatie over de SSO van de toepassing met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: multi-factor Authentication is een krachtig verificatie beheer, maar een opt-in-functie voor de gebruikers van de Azure stack Edge-service. Het kan worden gebruikt voor ondersteunde scenario's zoals rand beheer van Azure Stack edge-apparaten op de Azure Portal. Houd er rekening mee dat de lokale toegang tot de Azure Stack edge-apparaten geen ondersteuning biedt voor multi-factor Authentication.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: Schakel Azure monitor in om apparaat-of container logboeken voor uw Azure stack Edge-service te verzamelen. Controleer containers, zoals de toepassingen waarop meerdere Compute-apps worden uitgevoerd, op het Kubernetes-cluster op uw apparaat.

Daarnaast kan een ondersteunings pakket, dat audit Logboeken bevat, worden verzameld op de lokale webgebruikersinterface van het Azure Stack edge-apparaat. Houd er rekening mee dat het ondersteunings pakket niet beschikbaar is op de Azure Portal.
 
- [Azure Monitor op uw Azure Stack Edge Pro-apparaat inschakelen](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Een ondersteunings pakket verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Richt lijnen**: de voorwaardelijke toegang van Azure Active Directory (Azure AD) is een opt-in-functie voor verificatie voor Azure stack Edge-service. Azure Stack Edge-service is een bron in de Azure Portal waarmee u een Azure Stack Edge Pro-apparaat kunt beheren op verschillende geografische locaties. 

- [Wat is voorwaardelijke toegang?](../active-directory/conditional-access/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Onbedoelde blootstelling van referenties elimineren

**Richt lijnen**: Volg de aanbevolen procedures voor het beveiligen van referenties, zoals:

- De activerings sleutel die wordt gebruikt om het apparaat te activeren met de Azure Stack Edge-resource in Azure.
- Aanmeldings referenties voor toegang tot het Azure Stack edge-apparaat.
- Sleutel bestanden die het herstellen van Azure Stack edge-apparaat kunnen vergemakkelijken.
- Kanaal versleutelings sleutel

Roteer en synchroniseer vervolgens uw opslag account sleutels regel matig om uw opslag account te beschermen tegen onbevoegde gebruikers.

- [Azure Stack Edge Pro-beveiliging en gegevens bescherming](azure-stack-edge-security.md)

- [Opslagsleutels synchroniseren](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](/azure/security/benchmarks/security-controls-v2-privileged-access) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Richt lijnen**: Azure stack EDGE-oplossing heeft verschillende onderdelen met krachtige toegangs controles om de toegang tot essentiële Bedrijfs apparaten te beperken. Uw organisatie heeft een Enterprise Agreement (EA) of Cloud Solution Provider (CSP) of een Microsoft Azure Sponsorship-abonnement nodig om het apparaat te configureren en te beheren: 

Azure Stack Edge-service wordt beveiligd door de Azure-beveiligings functies als een beheer service die wordt gehost in Azure. U kunt de versleutelings sleutel voor uw resource ophalen in apparaateigenschappen voor elke Software Development Kit beheer bewerkingen, maar kan alleen de versleutelings sleutel weer geven als u de juiste machtigingen hebt voor de resource Graph API.

Het Azure Stack Edge Pro-apparaat is een on-premises apparaat waarmee u uw gegevens kunt transformeren door deze lokaal te verwerken en deze vervolgens naar Azure te verzenden. Uw apparaat:

- Er is een activerings sleutel nodig om toegang te krijgen tot de Azure Stack Edge-service.
- is te allen tijde beveiligd door een apparaatwachtwoord.
- beveiligd opstarten is ingeschakeld.

- is een vergrendeld apparaat. De Device Base Board Management Controller (BMC) en BIOS zijn beveiligd met een wacht woord. Het BIOS wordt beveiligd door beperkte gebruikers toegang.
- Windows Defender Device Guard wordt uitgevoerd. Met Device Guard kunt u alleen vertrouwde toepassingen uitvoeren die u in uw beleid voor code-integriteit definieert.

Volg de aanbevolen procedures voor het beveiligen van alle referenties en geheimen die worden gebruikt voor toegang tot Azure Stack Edge. 

- [Aanbevolen procedures voor wachtwoorden](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Hulp**: Azure stack Edge heeft een gebruiker met de naam ' EdgeUser ', waarmee het apparaat kan worden geconfigureerd. Het heeft ook Azure Resource Manager gebruiker ' EdgeArmUser ' voor de lokale Azure Resource Manager-functies op het apparaat. 

De aanbevolen procedures moeten worden gevolgd om de volgende zaken te beschermen:

- Referenties die worden gebruikt voor toegang tot het on-premises apparaat

- Referenties voor de SMB-share.

- Toegang tot client systemen die zijn geconfigureerd voor het gebruik van NFS-shares.

- Sleutels van het lokale opslag account die worden gebruikt voor toegang tot de lokale opslag accounts wanneer de BLOB-REST API wordt gebruikt.

Meer informatie vindt u op de koppeling waarnaar wordt verwezen.

- [Informatie over beveiliging voor uw Azure Stack edge-apparaten](azure-stack-edge-security.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richt lijnen**: beveiligde en geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik werk stations met een zeer beveiligde gebruikers met of zonder Azure Bastion voor beheer taken. Gebruik Azure Active Directory (Azure AD), micro soft Defender Advanced Threat Protection (ATP) en Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. 

De beveiligde werkstations kunnen centraal worden beheerd en beveiligde configuraties afdwingen, waaronder krachtige verificatie, software- en hardwarebasislijnen, beperkte logische toegang en netwerktoegang.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Een werkstation met uitgebreide toegang gebruiken](/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](/azure/security/benchmarks/security-controls-v2-data-protection) voor meer informatie.*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

**Hulp**: Azure stack Edge behandelt alle interactie gegevens als vertrouwelijk met alleen geautoriseerde gebruikers die toegang tot deze gegevens hebben. Volg de aanbevolen procedures voor het beveiligen van de referenties die worden gebruikt voor toegang tot Azure Stack Edge-service.

- [Azure Stack Edge Pro-beveiliging en gegevens bescherming](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

**Richt lijnen**: Azure stack Edge maakt gebruik van beveiligde kanalen voor gegevens in de vlucht. Deze zijn:

- Standaard TLS 1,2 wordt gebruikt voor gegevens die tussen het apparaat en de Azure-Cloud worden verzonden. Er is geen terugval voor TLS 1,1 en eerder. Agent communicatie wordt geblokkeerd als TLS 1,2 niet wordt ondersteund. TLS 1,2 is ook vereist voor het beheer van Azure Portal en Software Development Kit (SDK).

- Wanneer clients via de lokale webgebruikersinterface van een browser toegang hebben tot uw apparaat, wordt standaard TLS 1,2 gebruikt als het standaard-beveiligde Protocol

De best practice is het configureren van uw browser voor gebruik van TLS 1,2. Gebruik SMB 3,0 met versleuteling om gegevens te beveiligen wanneer u deze kopieert van uw gegevens servers.

- [Aanbevolen procedures voor het beveiligen van gegevens in Flight](azure-stack-edge-security.md#protect-data-in-flight)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](/azure/security/benchmarks/security-controls-v2-asset-management) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Houd er rekening mee dat er extra machtigingen nodig zijn om de werk belasting en services te kunnen zien. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: alleen gemachtigde gebruikers, bijvoorbeeld ' EdgeArmUser ', hebben toegang tot de api's van het Azure stack edge-apparaat via de lokale Azure Resource Manager. Wacht woorden van gebruikers accounts kunnen alleen worden beheerd op het Azure Portal. 

- [Wachtwoord instellen voor Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: alleen goedgekeurde toepassingen in reken bronnen gebruiken

**Richt lijnen**: u kunt uw eigen toepassingen laten uitvoeren op elke lokaal gemaakte virtuele machine. Gebruik Power shell-scripts om lokale Compute virtual machines te maken op uw stack-apparaat. We raden u ten zeerste aan om alleen vertrouwde toepassingen uit te voeren op de lokale virtuele machines. 

- [Uitvoering van Power shell-script beheren in Windows-omgeving](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1&amp;viewFallbackFrom=powershell-6&amp;preserve-view=true)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Hulp**: Azure stack Edge biedt geen mogelijkheden voor detectie van bedreigingen. Klanten kunnen echter audit logboeken verzamelen in een ondersteunings pakket voor het detecteren en analyseren van offline bedreigingen. 

- [Ondersteunings pakket voor Azure Stack edge-apparaat verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

**Hulp**: voor Azure stack Edge zijn netwerk controle logboeken ingeschakeld als onderdeel van het Download bare ondersteunings pakket. Deze logboeken kunnen worden geparseerd om een semi-real-time bewaking voor uw Azure Stack edge-apparaten te implementeren.

- [Azure Stack Edge een ondersteunings pakket verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Hulp**: realtime-bewaking met Logboeken wordt momenteel niet ondersteund voor Azure stack Edge. Er bestaat een mogelijkheid voor het verzamelen van een ondersteunings pakket waarmee u de verschillende logboeken kunt analyseren, zoals firewall, software, inbreuk op de hardware en systeem gebeurtenis logboeken voor uw Azure Stack Edge Pro-apparaat. Houd er rekening mee dat de inbraak van de software of de standaard firewall logboeken worden verzameld voor binnenkomend en uitgaand verkeer.

- [Een ondersteunings pakket voor Azure Stack Edge verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Hulp**: realtime-bewaking met Logboeken wordt momenteel niet ondersteund voor Azure stack Edge. U kunt echter een ondersteunings pakket verzamelen waarmee u de verschillende logboeken kunt analyseren.  Het ondersteunings pakket is gecomprimeerd en wordt gedownload naar het pad naar keuze. Pak het pakket uit en Bekijk de logboek bestanden van het systeem. U kunt deze logboeken ook verzenden naar een hulp programma voor gebeurtenis beheer van beveiligings gegevens of een andere centrale opslag locatie voor analyse.

- [Een ondersteunings pakket voor Azure Stack Edge verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Hulp**: de Bewaar periode voor logboek opslag kan niet worden gewijzigd op het Azure stack edge-apparaat. Oudere logboeken worden naar behoefte verwijderd. U kunt met regel matige intervallen ondersteunings pakketten van het apparaat verzamelen voor eventuele vereisten om de logboeken gedurende een langere periode te bewaren. 

- [Een ondersteunings pakket voor Azure Stack Edge verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: Azure stack Edge maakt gebruik van time.Windows.com, een netwerk tijd provider server van micro soft.  Met Azure Stack Edge kan de klant ook de netwerk tijd Protocol server van hun keuze configureren.

- [Instellingen voor de apparaatinstellingen van Azure Stack Edge configureren](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richt lijnen**: Zorg ervoor dat het antwoord plan voor bedrijfs incidenten processen bevat: 

- reageren op beveiligings incidenten

- die zijn bijgewerkt voor Azure-Cloud
 
- worden regel matig uitgeoefend om te zorgen voor gereedheid

U wordt aangeraden beveiliging te implementeren met behulp van gestandaardiseerde incident response processen in de bedrijfs omgeving.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit 

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit ervan te meten. Zo kunt u lessen uit eerdere incidenten ontdekken en waarschuwingen voor analisten prioriteiten geven om te voor komen dat er verspilde tijd verwerkte positieve waarschuwingen zijn. Bouw hoogwaardige waarschuwingen op basis van uw ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen voor het genereren en opschonen van waarschuwingen met logboek registratie en correlatie voor diverse waarschuwings bronnen. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. Maak geavanceerde waarschuwings regels met Azure Sentinel om automatische incidenten te genereren voor een onderzoek. 

Exporteer uw Security Center waarschuwingen en aanbevelingen naar Azure Sentinel met de export functie om Risico's voor Azure-resources te identificeren. Het is raadzaam om een proces te maken voor het exporteren van waarschuwingen en aanbevelingen op een geautomatiseerde manier voor continue beveiliging.

- [Export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detectie en analyse: een incident onderzoeken

**Richt lijnen**: Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken om een volledig overzicht te maken van de activiteit die heeft plaatsgevonden tijdens het onderzoeken van mogelijke incidenten. Diverse logboek typen moeten worden verzameld voor het bijhouden van de activiteiten van een mogelijke aanvaller in de Kill-keten om blinde vlekken te voor komen.  Zorg er ook voor dat inzichten en informatie worden vastgelegd voor historische referentie.  

De gegevensbronnen voor onderzoek zijn niet alleen de gecentraliseerde logboekbronnen die al worden verzameld door de services binnen het bereik en de actieve systemen, maar kunnen ook de volgende zijn:

- Netwerkgegevens: gebruik stroomlogboeken van netwerkbeveiligingsgroepen, Azure Network Watcher en Azure Monitor om logboeken van netwerkstromen en andere analysegegevens vast te leggen. 

- Momentopnamen van actieve systemen: 

    - Gebruik de functie voor het maken van momentopnamen van virtuele machines van Azure om een momentopname of snapshot te maken van de schijf van het actieve systeem. 

    - Kies de systeem eigen geheugen dump mogelijkheid van het besturings systeem om een moment opname te maken van het actieve systeem geheugen.

    - Gebruik de functie snap shot van de Azure-Services of een software-mogelijkheid van derden om moment opnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide voorzieningen voor gegevensanalyse voor vrijwel elke logboekbron en een portal voor dossierbeheer om de volledige levenscyclus van incidenten te beheren. Informatie die wordt verzameld tijdens een onderzoek kan worden gekoppeld aan een incident voor tracerings- en rapportagedoeleinden. 

- [Momentopname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentopname maken van de schijf van een Linux-computer](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Diagnostische gegevens en geheugendumps van Microsoft Azure Ondersteuning](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied op basis van de ernst van waarschuwingen en de gevoeligheid van een asset context aan analisten zodat deze weten op welke incidenten ze zich het eerst moeten richten. Gebruik de toegewezen Ernst op elke waarschuwing van Azure Security Center om u te helpen bepalen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoeveel vertrouwen Security Center heeft in de zoekactie of de analysefunctie die is gebruikt om de waarschuwing te genereren, evenals in welke mate kan worden aangetoond dat er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, uitschakeling en herstel: het afhandelen van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige, terugkerende taken om de responstijd te versnellen en de werklast van analisten te verlichten. Het uitvoeren van handmatige taken duurt langer, waardoor elk incident trager gaat en het aantal incidenten dat een analist kan afhandelen, kleiner wordt. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="posture-and-vulnerability-management"></a>Beveiligingspostuur en beveiligingsproblemen beheren

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management) voor meer informatie.*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: veilige configuraties voor reken bronnen instellen

**Hulp**: Azure stack Edge biedt ondersteuning voor het maken van beveiligde configuraties voor de lokale virtuele machines die door de klanten worden gemaakt. Het wordt ten zeerste aangeraden micro soft richt lijnen te gebruiken voor het instellen van beveiligings basislijnen tijdens het maken van lokale virtuele machines,

- [De beveiligings basislijnen downloaden die zijn opgenomen in de Security compliantie Toolkit](/windows/security/threat-protection/windows-security-baselines)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: beveiligde configuraties voor reken bronnen

**Hulp**: Azure stack Edge biedt geen ondersteuning voor beveiligde configuraties voor de lokale virtuele machines die door de klanten zijn gemaakt. Het wordt ten zeerste aanbevolen om de Security compliantie Toolkit (SCT) te gebruiken om beveiligde configuraties voor hun reken bronnen te krijgen.

Host-besturings systeem en virtuele machines die worden beheerd door Azure Stack Edge, behouden hun beveiligings configuraties. 

- [Windows-beveiligings basislijnen](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>HW-5: Sla het aangepaste besturings systeem en de container installatie kopieën veilig op

**Hulp**: host-besturings systemen en virtuele machines die worden beheerd door Azure stack Edge, worden veilig opgeslagen. 

Klanten kunnen hun eigen installatie kopieën van virtuele machines en containers meebrengen en verantwoordelijk zijn voor hun beveiligde beheer.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Beveiligingsproblemen in software snel en automatisch oplossen

**Hulp**: Azure stack Edge biedt regel matige patch-updates en klanten van waarschuwingen wanneer dergelijke updates beschikbaar zijn voor het oplossen van beveiligings problemen. Het is de verantwoordelijkheid van de klant om hun apparaten en virtuele machines (gemaakt door hen) up-to-date te houden met de meest recente patches.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="endpoint-security"></a>Endpoint Security

*Zie [Azure Security Bench Mark: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)(Engelstalig) voor meer informatie.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: eindpunt detectie en-antwoord gebruiken (EDR)

**Hulp**: Azure stack Edge biedt geen ondersteuning voor het rechtstreeks ondersteunen van eindpunt detectie en-antwoorden (EDR). U kunt echter een ondersteunings pakket verzamelen en audit logboeken ophalen. Deze logboeken kunnen vervolgens worden geanalyseerd om te controleren op afwijkende activiteiten. 

- [Ondersteunings pakket voor Azure Stack edge-apparaat verzamelen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: centraal beheerde moderne anti-malware-software gebruiken

**Richt lijnen**: Azure stack Edge maakt gebruik van Windows Defender Application Control (WDAC) met een beleid voor strikte code-integriteit (CI) waarmee alleen vooraf bepaalde toepassingen en scripts kunnen worden uitgevoerd. Windows Defender real time Protection (RTP) anti-malware is ook ingeschakeld. Klant kan ervoor kiezen om anti-malware uit te voeren in de bewerkings-Vm's die ze hebben gemaakt om lokaal op Azure Stack edge-apparaat te worden uitgevoerd.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Benchmark: back-up en herstel](/azure/security/benchmarks/security-controls-v2-backup-recovery) voor meer informatie.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: controleren op regel matige automatische back-ups

**Richt lijnen**: periodieke back-ups van uw Azure stack edge-apparaat worden aanbevolen en kunnen worden uitgevoerd met Azure backup en andere oplossingen voor gegevens beveiliging van derden om de gegevens te beschermen die zijn opgenomen in de virtuele machines die op het apparaat zijn geïmplementeerd. 

Oplossingen voor gegevens beveiliging van derden, zoals Cohesity, CommVault en Veritas, kunnen ook een back-upoplossing bieden voor de gegevens in de lokale SMB-of NFS-shares,. 

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Een apparaatfout voorbereiden](azure-stack-edge-gpu-prepare-device-failure.md)

- [Bestanden en mappen op Vm's beveiligen](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="br-2-encrypt-backup-data"></a>BR-2: back-upgegevens versleutelen

**Richt lijnen**: Zorg ervoor dat uw back-ups worden beschermd tegen aanvallen. Hierbij moet de back-ups worden versleuteld om te worden beveiligd tegen verlies van vertrouwelijkheid.  Raadpleeg de gewenste back-upoplossing voor meer informatie.

- [Gegevens in Edge-lokale shares beveiligen](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Bestanden en mappen op virtuele machines beveiligen](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Valideer alle back-ups, inclusief door de klant beheerde sleutels

**Hulp**: periodiek gegevens herstel van uw back-ups uitvoeren. 

- [Herstel procedures voor de Azure Stack rand](azure-stack-edge-gpu-recover-device-failure.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Het risico op verloren sleutels beperken

**Richt lijnen**: Zorg ervoor dat alle Azure stack Edge-back-ups, inclusief alle door de klant beheerde sleutels, worden beschermd in overeenstemming met de aanbevolen procedures voor organisaties. Uw Azure Stack edge-apparaat is gekoppeld aan een Azure Storage-account dat wordt gebruikt als bestemmings opslagplaats voor uw gegevens in Azure. 

Toegang tot het Azure Storage-account wordt beheerd door Azure-abonnementen en de 2 512-bits opslag toegangs sleutels die aan dat opslag account zijn gekoppeld. Een van de toegangs sleutels wordt gebruikt voor verificatie doeleinden wanneer het Azure Stack edge-apparaat toegang heeft tot het opslag account. De andere sleutel wordt gereserveerd voor de periodieke rotatie van de sleutel. Zorg ervoor dat aanbevolen beveiligings procedures worden gebruikt voor het draaien van sleutels.

- [Azure Stack Edge-apparaatgegevens in de Azure Storage accounts beveiligen](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Benchmark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor asset-management en gegevensbescherming definiëren 

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor de continue bewaking en bescherming van systemen en gegevens schriftelijk vastlegt en bekendmaakt. Ken hogere prioriteiten toe aan de detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

-   Inzicht van beveiligingsorganisaties in risico's en asset-inventaris 

-   Goedkeuring door beveiligingsorganisaties van Azure-services voor gebruik 

-   Beveiliging van assets op grond van hun levenscyclus

-   Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

-   Gebruik van systeemeigen beveiligingsmogelijkheden van Azure en van derde partijen

-   Vereisten voor gegevensversleuteling in gebruiksscenario's met gegevens tijdens een overdracht en data-at-rest

-   Juiste cryptografische standaarden

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-controls-v2-asset-management) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection) (Azure Security Benchmark: gegevensbeveiliging)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie van bedrijf definiëren 

**Richtlijnen**: Stel een bedrijfsstrategie op om de toegang tot assets te segmenteren door een combinatie van besturingselementen voor het beheer van identiteiten, netwerken, toepassingen, abonnementen, beheergroepen en andere besturingselementen te gebruiken.

Zorg dat u een nauwgezette afweging maakt tussen de noodzaak om de beveiliging van de rest te scheiden en de noodzaak om systemen die met elkaar moeten kunnen communiceren en toegang moeten hebben tot gegevens, in staat te stellen om hun dagelijkse werklast uit te voeren.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle typen besturingselementen, zoals voor netwerkbeveiliging, identiteits- en toegangsmodellen, toepassingsbevoegdheden/toegangsmodellen evenals besturingselementen voor door mensen uitgevoerde processen.

- [Richtlijnen voor segmentatiestrategie in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richtlijnen voor segmentatiestrategie in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerksegmentatie afstemmen op segmentatiestrategie van bedrijf](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor het beheer van beveiligingspostuur definiëren

**Richtlijnen**: Meet en beperk voortdurend de risico's die alle individuele assets en de omgeving die ze hosten, lopen. Ken hogere prioriteiten toe aan hoogwaardige assets en assets die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten voor binnenkomend en uitgaand netwerkverkeer, gebruikers- en beheerderseindpunten enzovoort.

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Organisatierollen, verantwoordelijkheden en aansprakelijkheden afstemmen

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligingsorganisatie schriftelijk vastlegt en bekendmaakt. Ken een hoge prioriteiten toe aan het verschaffen van duidelijkheid over wie aansprakelijk is voor beveiligingsbeslissingen. Bied opleidingen aan iedereen over het gedeelde verantwoordelijkheidsmodel en biedt technische teams trainingen over technologie ter beveiliging van de cloud.

- [Best practice voor Azure-beveiliging 1: personen: Teams trainen inzake het cloudbeveiligingstraject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Best practice voor Azure-beveiliging 2: personen: Teams trainen inzake cloudbeveiligingstechnologie](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - proces: Aansprakelijkheid toewijzen voor beslissingen over cloudbeveiliging](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Strategie voor netwerkbeveiliging definiëren

**Richtlijnen**: Stel een benadering op voor netwerkbeveiliging in Azure als onderdeel van de algehele strategie voor toegangsbeheer in uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerkbeheer en verantwoordelijkheid voor beveiliging

-   Model voor segmentatie van virtueel netwerk afgestemd op segmentatiestrategie van bedrijf

-   Herstelstrategie voor verschillende scenario's met bedreigingen en aanvallen

-   Strategie voor internetrand en inkomend en uitgaand verkeer

-   Strategie voor hybride cloud- en on-premises interconnectiviteit

-   Up-to-date netwerkbeveiligingsartefacten (zoals netwerkdiagrammen, architectuur van referentienetwerk)

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/)

- [Overzicht van Azure-netwerkbeveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor architectuur van bedrijfsnetwerk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor het gebruik van identiteiten en uitgebreide toegang definiëren

**Richtlijnen**: Stel een benadering op voor het gebruik van identiteiten en uitgebreide toegang als onderdeel van de algehele strategie voor toegangsbeheer in uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits- en verificatiesysteem en bijbehorende interconnectiviteit met andere interne en externe identiteitssystemen

-   Krachtige verificatiemethoden in verschillende gebruiksscenario's en onder verschillende voorwaarden

-   Bescherming van gebruikers met zeer uitgebreide bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikersactiviteiten  

-   Proces voor het beoordelen en op elkaar afstemmen van de identiteit en toegangsrechten van gebruikers

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: Identity management](/azure/security/benchmarks/security-controls-v2-identity-management) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-controls-v2-privileged-access) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons op bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en respons op bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl tegelijkertijd aan alle nalevingsvereisten wordt voldaan. Ken een hoge prioriteit toe aan het bieden van waarschuwingen van hoge kwaliteit en naadloze ervaringen aan analisten, zodat ze zich kunnen concentreren op bedreigingen in plaats van op integraties en handmatig uit te voeren stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de personen of groepen die binnen de organisatie beveiligingsactiviteiten (SecOps) uitvoeren 

-   Een goed gedefinieerd proces voor het reageren op incidenten dat is afgestemd op het NIST of een ander framework binnen de branche 

-   Logboekregistratie en retentie om ondersteuning te bieden voor de detectie van bedreigingen, het reageren op incidenten en het naleven van regelgeving

-   Informatie en correlatiegegevens over bedreigingen die centraal beschikbaar zijn via SIEM, systeemeigen Azure-mogelijkheden en andere bronnen 

-   Communicatie- en meldingenplan met uw klanten, leveranciers en openbaar belanghebbenden

-   Gebruik van systeemeigen Azure-platforms en platforms van derden voor het afhandelen van incidenten, zoals logboekregistratie en detectie van bedreigingen, forensische onderzoeken en het oplossen en uitschakelen van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en het bewaren van bewijsmateriaal

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
