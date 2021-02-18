---
title: Azure-beveiligings basislijn voor virtuele WAN
description: De basis lijn voor virtuele WAN-beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f487467b08332eea4ee19a7fb8836d843bd254f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582645"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azure-beveiligings basislijn voor virtuele WAN

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) toegepast op Microsoft Azure virtuele WAN. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op virtuele WAN. **Besturings elementen** die niet van toepassing zijn op virtuele WAN, zijn uitgesloten.

Zie het [volledige bestand met de baseline-toewijzing van virtuele WAN-beveiliging](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe virtuele WAN helemaal is toegewezen aan de Azure Security-Bench Mark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-controls-v2-network-security.md) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp**: Microsoft Azure virtuele WAN biedt aangepaste routerings mogelijkheden en biedt versleuteling voor uw ExpressRoute-verkeer. Alle routebeheer wordt verzorgd door de virtuele-hub-router, die ook transitconnectiviteit tussen virtuele netwerken mogelijk maakt. Het versleutelen van uw ExpressRoute-verkeer met Virtual WAN zorgt voor een versleutelde door Voer tussen de on-premises netwerken en Azure Virtual Networks via ExpressRoute, zonder het open bare Internet te passeren of open bare IP-adressen te gebruiken. 

- [ExpressRoute verkeers versleuteling](virtual-wan-about.md#encryption)

- [Privé-koppeling in virtuele WAN gebruiken](howto-private-link.md)

- [Aangepaste routerings scenario's](scenario-any-to-any.md)

- [Documentatie voor de aangepaste route tabel](how-to-virtual-hub-routing.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden 

**Hulp**: Microsoft Azure ExpressRoute biedt een persoonlijke verbinding met Azure Virtual WAN. Omdat de ExpressRoute-verbindingen niet via het open bare Internet gaan, biedt ExpressRoute meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. U kunt ook een virtueel particulier netwerk gebruiken om verbinding te maken met Azure via een site-naar-site (S2S) VPN of een punt-naar-site (P2S) VPN.

- [ExpressRoute in virtuele WAN](virtual-wan-expressroute-portal.md)

- [Overzicht van site-naar-site-VPN](virtual-wan-site-to-site-portal.md)

- [Site-VPN-overzicht](virtual-wan-point-to-site-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Hulp**: virtuele WAN maakt geen eind punten beschikbaar voor externe netwerken die moeten worden beveiligd met conventionele netwerk beveiliging. U kunt resources in spoke Virtual Networks (elk virtueel netwerk dat is verbonden met een virtuele hub) beveiligen met behulp van virtuele netwerk beveiligings Services. 

Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. 

Kies Azure-verschaft DDoS Protection om uw assets te beschermen tegen aanvallen op uw Azure Virtual Networks. Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die zijn gerelateerd aan uw netwerkgerelateerde bronnen.

- [Documentatie over Azure Firewall](../firewall/index.yml)

- [Azure DDoS Protection Standard beheren met de Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Richt lijnen**: Virtual WAN is een door micro soft beheerde service. Het biedt geen systeem eigen indringings detectie of mogelijkheden voor het voor komen van indringers. Er zijn echter beveiligings mogelijkheden beschikbaar voor virtuele WAN via Azure Firewall om een uniform Point of Policy-besturings element in te scha kelen. U kunt een Azure Firewall beleid maken en het beleid koppelen aan een virtuele WAN-hub zodat de bestaande virtuele WAN-hub kan functioneren als een beveiligde virtuele hub, met de vereiste Azure Firewall resources zijn geïmplementeerd.

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Richt lijnen**: Vereenvoudig de netwerk beveiligings regels door gebruik te maken van Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Door de servicetag naam op te geven in het bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Toepassings beveiligings groepen begrijpen en gebruiken](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Documentatie over Azure Firewall](../firewall/index.yml)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: secure Domain Name Service (DNS)

**Hulp**: veilige DNS-mogelijkheden worden aan Virtual WAN door gegeven met Azure firewall. Configureer Azure Firewall om te fungeren als een DNS-proxy die een intermediair wordt voor DNS-aanvragen van virtuele client machines naar een DNS-server. Voor aangepaste DNS-server configuraties schakelt u DNS-proxy in om te voor komen dat een DNS-omzetting overeenkomt en schakelt u Fully Qualified Domain Name filtering in de netwerk regels in. 

- [Documentatie over Azure Firewall](../firewall/index.yml)

- [DNS-instellingen Azure Firewall](../firewall/dns-settings.md)

- [Azure Firewall gebruiken als een DNS-doorstuur server met een persoonlijke koppeling](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](../security/benchmarks/security-controls-v2-identity-management.md) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Hulp**: Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer voor Azure-Services. inclusief Virtual WAN. Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, Azure Virtual Machines (Linux en Windows), Azure Key Vault, platform as a Service (PaaS) en software as a Service (SaaS)-toepassingen.
- De resources van uw organisatie, zoals toepassingen op Azure of uw bedrijfs netwerk bronnen

Beveilig Azure AD als een hoge prioriteit in de Cloud beveiligings procedure van uw organisatie. Evalueer uw identiteits-en beveiligings postuur met de functie beveiligings Score van Security Center om te meten hoe nauw keurig uw configuratie overeenkomt met de best practice aanbevelingen van micro soft. Implementeer zonodig de best practice aanbevelingen van micro soft voor verbeteringen in uw beveiligings postuur.

Azure AD biedt ook ondersteuning voor externe identiteiten, waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit. 

Bekijk informatie over het gebruik van Azure AD in punt-naar-site-VPN-scenario's op de koppelingen waarnaar wordt verwezen.

- [Een Azure Active Directory-Tenant (AD) maken voor P2S OpenVPN-protocol verbindingen](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory-verificatie voor gebruikers-VPN configureren](virtual-wan-point-to-site-azure-ad.md)

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: momenteel wordt verificatie van Azure Active Directory (Azure AD) geleverd via integratie met een virtueel WAN punt-naar-site-VPN.

Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer voor Azure-Services. Azure AD biedt ondersteuning voor sterke verificatie-besturings elementen met meervoudige verificatie en krachtige methoden met een wacht woord.

Azure AD adviseert het volgende voor sterke verificatie-besturings elementen:

- Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg aanbevelingen voor identiteits-en toegangs beheer in Azure Security Center voor aanbevolen beveiligings procedures. Multi-factor Authentication afdwingen op alle, gebruikers of op per gebruikers niveau selecteren op basis van aanmeldings voorwaarden en risico factoren

- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar. Dit zijn onder andere, Windows hello voor bedrijven, Microsoft Authenticator-apps en on-premises verificatie methoden zoals Smart Cards

Zorg ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt voor beheerders en bevoegde gebruikers, gevolgd door een implementatie van een krachtig verificatie beleid voor andere gebruikers.

- [MFA inschakelen in P2S VPN voor virtuele WAN](openvpn-azure-ad-mfa.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in voor VPN-gebruikers (punt-naar-site) met behulp van Azure AD-verificatie. Multi-factor Authentication op basis van gebruikers configureren of gebruikmaken van multi-factor Authentication met voorwaardelijke toegang. Met voorwaardelijke toegang kunt u nauw keuriger bepalen hoe een tweede factor moet worden bevorderd. Hiermee kan multi-factor Authentication alleen worden toegewezen aan VPN en kunnen andere toepassingen worden uitgesloten die zijn gekoppeld aan de Azure AD-Tenant. 

Houd er rekening mee dat Azure AD-verificatie alleen beschikbaar is voor gateways met behulp van OpenVPN-protocol en clients met Windows.

- [Wat is voorwaardelijke toegang](../active-directory/conditional-access/overview.md)

- [Azure Active Directory-verificatie voor gebruikers-VPN configureren](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Onbedoelde blootstelling van referenties elimineren

**Richt lijnen**: voor site-naar-site VPN in virtuele WAN wordt gebruikgemaakt van vooraf gedeelde sleutels (PSK) die worden gedetecteerd, gemaakt en beheerd door de klant in hun Azure Key Vault. Referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Voor GitHub kunt u de systeemeigen functie voor het scannen op geheimen gebruiken om referenties of een andere vormen van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Scannen op geheimen in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](../security/benchmarks/security-controls-v2-privileged-access.md) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Hulp**: Azure Virtual WAN maakt gebruik van Azure-functies voor toegangs beheer op basis van rollen (Azure RBAC) voor het isoleren van toegang tot bedrijfskritische systemen door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Beperk ook de toegang tot de beheer-, identiteits-en beveiligings systemen met beheerders toegang tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers, beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-controls-v2-data-protection.md) voor meer informatie.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

**Richt lijnen**: gebruik punt-naar-site VPN, site-naar-site-VPN en versleutelde Express route met Virtual WAN voor uw connectiviteits vereisten. VPN-versleuteling beveiligt gegevens in transit van ' buiten-band-aanvallen (zoals verkeer vastleggen) om te voor komen dat aanvallers de gegevens kunnen lezen of wijzigen. 

- [Punt-naar-site-VPN](virtual-wan-point-to-site-portal.md)

- [Site-naar-site-VPN](virtual-wan-site-to-site-portal.md)

- [Versleutelde ExpressRoute](vpn-over-expressroute.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](../security/benchmarks/security-controls-v2-asset-management.md) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Daarom moeten beveiligings inzichten en-risico's altijd centraal worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie. Azure Virtual WAN biedt ook ondersteuning voor op Azure Resource Manager gebaseerde implementaties van resources waarmee u Asset-sjablonen kunt exporteren. 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Hulp**: gebruik Azure monitor om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te geven om met Azure-resources te communiceren te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../security/benchmarks/security-controls-v2-logging-threat-detection.md) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Richt lijnen**: punt-naar-site-VPN met Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD). Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor-, Azure Sentinel-, SIEM-of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse-gebruiks voorbeelden. Deze zijn:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Gebruik Azure Security Center om waarschuwingen te maken voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen, waaronder afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kunt u met behulp van Security Center de module Threat Protection een uitgebreidere beveiligings waarschuwing verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL data base en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in accountafwijkingen binnen de afzonderlijke resources.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

**Richt lijnen**: punt-naar-site-VPN met Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD). Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor-, Azure Sentinel-, SIEM-of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse-gebruiks voorbeelden. Deze zijn:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Gebruik Azure Security Center om waarschuwingen te maken voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen, waaronder afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kunt u met behulp van Security Center de module Threat Protection een uitgebreidere beveiligings waarschuwing verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL data base en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in accountafwijkingen binnen de afzonderlijke resources.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

**Richt lijnen**: Bewaak Azure Virtual WAN met Azure monitor. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. Vermeldingen in het activiteiten logboek worden standaard verzameld en kunnen worden weer gegeven in de Azure Portal. U kunt Azure-activiteiten Logboeken (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement.

Er zijn ook verschillende Diagnostische logboeken beschikbaar voor virtuele WAN en kunnen worden geconfigureerd voor de virtuele WAN-resource met Azure Portal.  U kunt ervoor kiezen om te verzenden naar Log Analytics, streamen naar een Event Hub of eenvoudigweg te archiveren naar een opslag account. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

- [Azure Firewall-logboeken en metrische gegevens](../firewall/logs-and-metrics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Richt lijnen**: activiteiten logboeken van Azure, die automatisch worden ingeschakeld, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw virtuele WAN-resources van Azure, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden tijdens het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel Azure-resource Logboeken in voor virtuele WAN. U kunt Azure Security Center en Azure Policy gebruiken om resource logboeken en het verzamelen van logboek gegevens in te scha kelen. Deze logboeken kunnen essentieel zijn voor het later onderzoeken van beveiligings incidenten en het uitvoeren van forensische-oefeningen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Hulp** bij het inschakelen van beveiligings logboek registratie voor virtuele WAN met Azure monitor. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. Vermeldingen in het activiteiten logboek worden standaard verzameld en kunnen worden weer gegeven in de Azure Portal. U kunt Azure-activiteiten Logboeken (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement. 

Er zijn ook verschillende Diagnostische logboeken beschikbaar voor virtuele WAN en kunnen worden geconfigureerd voor de virtuele WAN-resource met Azure Portal. Verzenden naar Log Analytics, streamen naar een Event Hub of eenvoudigweg archiveren naar een opslag account. Daarnaast schakelt u gegevens in voor Azure Sentinel of een Security Information and Event Management-oplossing van derden. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

- [Azure Firewall-logboeken en metrische gegevens](../firewall/logs-and-metrics.md)

Azure Virtual WAN-beveiliging wordt via Azure Firewall verschaft. 

- [Documentatie over Azure Firewall](../firewall/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Richt lijnen**: de Bewaar periode van het logboek configureren volgens uw naleving, regelgeving en zakelijke vereisten. In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center-waarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-controls-v2-incident-response.md) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligingsincidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse - incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u leren van eerdere incidenten en prioriteit toekennen aan waarschuwingen voor analisten, zodat ze geen tijd verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen met eerdere incidenten, gevalideerde communitybronnen, en tools die zijn ontworpen om waarschuwingen te genereren en op te schonen door verschillende signaalbronnen samen te voegen en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit voor diverse Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detectie en analyse - een incident onderzoeken

**Richtlijnen**: Zorg ervoor dat analisten verschillende gegevensbronnen kunnen bevragen en gebruiken bij het onderzoeken van mogelijke incidenten, zodat ze een volledig beeld hebben van wat er is gebeurd. Er moeten verschillende logboeken worden verzameld om de activiteiten van een mogelijke aanvaller in de kill chain te volgen om zo blinde vlekken te voorkomen.  U moet er ook voor zorgen dat inzichten en resultaten worden vastgelegd voor andere analisten, zodat deze kunnen worden geraadpleegd als historische naslaginformatie.  

De gegevensbronnen voor onderzoek zijn niet alleen de gecentraliseerde logboekbronnen die al worden verzameld door de services binnen het bereik en de actieve systemen, maar kunnen ook de volgende zijn:

- Netwerkgegevens: gebruik stroomlogboeken van netwerkbeveiligingsgroepen, Azure Network Watcher en Azure Monitor om logboeken van netwerkstromen en andere analysegegevens vast te leggen. 

- Momentopnamen van actieve systemen: 

    - Gebruik de functie voor het maken van momentopnamen van virtuele machines van Azure om een momentopname of snapshot te maken van de schijf van het actieve systeem. 

    - Gebruik de voorziening van het besturingssysteem voor het maken van geheugendumps om een momentopname te maken van het geheugen van het actieve systeem.

    - Gebruik de momentopnamefunctie van de Azure-services of van uw software om momentopnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide voorzieningen voor gegevensanalyse voor vrijwel elke logboekbron en een portal voor dossierbeheer om de volledige levenscyclus van incidenten te beheren. Informatie die wordt verzameld tijdens een onderzoek kan worden gekoppeld aan een incident voor tracerings- en rapportagedoeleinden. 

- [Momentopname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentopname maken van de schijf van een Linux-computer](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Diagnostische gegevens en geheugendumps van Microsoft Azure Ondersteuning](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoeveel vertrouwen Security Center heeft in de zoekactie of de analysefunctie die is gebruikt om de waarschuwing te genereren, evenals in welke mate kan worden aangetoond dat er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

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

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md) voor meer informatie.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="endpoint-security"></a>Endpoint Security

*Zie [Azure Security Bench Mark: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md)(Engelstalig) voor meer informatie.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: eindpunt detectie en-antwoord gebruiken (EDR)

**Richt lijnen**: klanten mogen geen instellingen voor het configureren van het eind punt detecteren en antwoorden. De Virtual Machines die in de Azure Virtual WAN-aanbieding wordt gebruikt, maken echter gebruik van deze mogelijkheden. Meer informatie over deze algemene mogelijkheden vindt u op de koppelingen waarnaar wordt verwezen. 

- [Overzicht van micro soft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Micro soft Defender ATP-service voor Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Micro soft Defender ATP-service voor niet-Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Benchmark: governance en strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor asset-management en gegevensbescherming definiëren 

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor de continue bewaking en bescherming van systemen en gegevens schriftelijk vastlegt en bekendmaakt. Ken hogere prioriteiten toe aan de detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

-   Inzicht van beveiligingsorganisaties in risico's en asset-inventaris 

-   Goedkeuring door beveiligingsorganisaties van Azure-services voor gebruik 

-   Beveiliging van assets op grond van hun levenscyclus

-   Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

-   Gebruik van voorzieningen voor gegevensbescherming van Azure en derden

-   Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

-   Juiste cryptografische standaarden

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](../security/benchmarks/security-controls-v2-asset-management.md) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](../security/benchmarks/security-controls-v2-data-protection.md) (Azure Security Benchmark: gegevensbeveiliging)

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

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-controls-v2-network-security.md)

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

- [Azure Security Benchmark: Identity management](../security/benchmarks/security-controls-v2-identity-management.md) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](../security/benchmarks//security-controls-v2-privileged-access.md) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons op bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Stel prioriteiten om analisten te voorzien van waarschuwingen van hoge kwaliteit en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en handmatige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de personen of groepen die binnen de organisatie beveiligingsactiviteiten (SecOps) uitvoeren 

-   Een goed gedefinieerd proces voor het reageren op incidenten dat is afgestemd op het NIST of een ander framework binnen de branche 

-   Logboekregistratie en retentie om ondersteuning te bieden voor de detectie van bedreigingen, het reageren op incidenten en het naleven van regelgeving

-   Informatie en correlatiegegevens over bedreigingen die centraal beschikbaar zijn via SIEM, systeemeigen Azure-mogelijkheden en andere bronnen 

-   Communicatie- en meldingenplan met uw klanten, leveranciers en openbaar belanghebbenden

-   Gebruik van systeemeigen Azure-platforms en platforms van derden voor het afhandelen van incidenten, zoals logboekregistratie en detectie van bedreigingen, forensische onderzoeken en het oplossen en uitschakelen van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en het bewaren van bewijsmateriaal

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
