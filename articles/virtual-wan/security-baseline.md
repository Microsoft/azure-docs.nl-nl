---
title: Azure-beveiligings basislijn voor virtuele WAN
description: De basis lijn voor virtuele WAN-beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029044"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azure-beveiligings basislijn voor virtuele WAN

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) toegepast op Microsoft Azure virtuele WAN. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op virtuele WAN. **Besturings elementen** die niet van toepassing zijn op virtuele WAN, zijn uitgesloten.

Zie het [volledige bestand met de baseline-toewijzing van virtuele WAN-beveiliging](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe virtuele WAN helemaal is toegewezen aan de Azure Security-Bench Mark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp**: Microsoft Azure virtuele WAN biedt aangepaste routerings mogelijkheden en biedt versleuteling voor uw ExpressRoute-verkeer. Alle routebeheer wordt verzorgd door de virtuele-hub-router, die ook transitconnectiviteit tussen virtuele netwerken mogelijk maakt. Het versleutelen van uw ExpressRoute-verkeer met Virtual WAN zorgt voor een versleutelde door Voer tussen de on-premises netwerken en Azure Virtual Networks via ExpressRoute, zonder het open bare Internet te passeren of open bare IP-adressen te gebruiken. 

- [ExpressRoute verkeers versleuteling](virtual-wan-about.md#encryption)

- [Privé-koppeling in virtuele WAN gebruiken](howto-private-link.md)

- [Aangepaste routerings scenario's](scenario-any-to-any.md)

- [Documentatie voor de aangepaste route tabel](how-to-virtual-hub-routing.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden 

**Hulp**: Microsoft Azure ExpressRoute biedt een persoonlijke verbinding met Azure Virtual WAN. Omdat de ExpressRoute-verbindingen niet via het open bare Internet gaan, biedt ExpressRoute meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. U kunt ook een virtueel particulier netwerk gebruiken om verbinding te maken met Azure via een site-naar-site (S2S) VPN of een punt-naar-site (P2S) VPN.

- [ExpressRoute in virtuele WAN](virtual-wan-expressroute-portal.md)

- [Overzicht van site-naar-site-VPN](virtual-wan-site-to-site-portal.md)

- [Site-VPN-overzicht](virtual-wan-point-to-site-portal.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Hulp**: virtuele WAN maakt geen eind punten beschikbaar voor externe netwerken die moeten worden beveiligd met conventionele netwerk beveiliging. U kunt resources in spoke Virtual Networks (elk virtueel netwerk dat is verbonden met een virtuele hub) beveiligen met behulp van virtuele netwerk beveiligings Services. 

Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. 

Kies Azure-verschaft DDoS Protection om uw assets te beschermen tegen aanvallen op uw Azure Virtual Networks. Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die zijn gerelateerd aan uw netwerkgerelateerde bronnen.

- [Documentatie over Azure Firewall](/azure/firewall)

- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Richt lijnen**: Virtual WAN is een door micro soft beheerde service. Het biedt geen systeem eigen indringings detectie of mogelijkheden voor het voor komen van indringers. Er zijn echter beveiligings mogelijkheden beschikbaar voor virtuele WAN via Azure Firewall om een uniform Point of Policy-besturings element in te scha kelen. U kunt een Azure Firewall beleid maken en het beleid koppelen aan een virtuele WAN-hub zodat de bestaande virtuele WAN-hub kan functioneren als een beveiligde virtuele hub, met de vereiste Azure Firewall resources zijn geïmplementeerd.

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Richt lijnen**: Vereenvoudig de netwerk beveiligings regels door gebruik te maken van Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Door de servicetag naam op te geven in het bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Toepassings beveiligings groepen begrijpen en gebruiken](/azure/virtual-network/security-overview#application-security-groups)

- [Documentatie over Azure Firewall](/azure/firewall/)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: secure Domain Name Service (DNS)

**Hulp**: veilige DNS-mogelijkheden worden aan Virtual WAN door gegeven met Azure firewall. Configureer Azure Firewall om te fungeren als een DNS-proxy die een intermediair wordt voor DNS-aanvragen van virtuele client machines naar een DNS-server. Voor aangepaste DNS-server configuraties schakelt u DNS-proxy in om te voor komen dat een DNS-omzetting overeenkomt en schakelt u Fully Qualified Domain Name filtering in de netwerk regels in. 

- [Documentatie over Azure Firewall](/azure/firewall/)

- [DNS-instellingen Azure Firewall](/azure/firewall/dns-settings)

- [Azure Firewall gebruiken als een DNS-doorstuur server met een persoonlijke koppeling](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Bench Mark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)(Engelstalig) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Chat-1: Azure Active Directory standaardiseren als het centrale identiteits-en verificatie systeem

**Hulp**: Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer voor Azure-Services. inclusief Virtual WAN. Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, Azure Virtual Machines (Linux en Windows), Azure Key Vault, platform as a Service (PaaS) en software as a Service (SaaS)-toepassingen.
- De resources van uw organisatie, zoals toepassingen op Azure of uw bedrijfs netwerk bronnen

Beveilig Azure AD als een hoge prioriteit in de Cloud beveiligings procedure van uw organisatie. Evalueer uw identiteits-en beveiligings postuur met de functie beveiligings Score van Security Center om te meten hoe nauw keurig uw configuratie overeenkomt met de best practice aanbevelingen van micro soft. Implementeer zonodig de best practice aanbevelingen van micro soft voor verbeteringen in uw beveiligings postuur.

Azure AD biedt ook ondersteuning voor externe identiteiten, waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit. 

Bekijk informatie over het gebruik van Azure AD in punt-naar-site-VPN-scenario's op de koppelingen waarnaar wordt verwezen.

- [Een Azure Active Directory-Tenant (AD) maken voor P2S OpenVPN-protocol verbindingen](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory-verificatie voor gebruikers-VPN configureren](virtual-wan-point-to-site-azure-ad.md)

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>Chat-4: gebruik sterke verificatie-instellingen voor alle toegang op basis van Azure Active Directory

**Richt lijnen**: momenteel wordt verificatie van Azure Active Directory (Azure AD) geleverd via integratie met een virtueel WAN punt-naar-site-VPN.

Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer voor Azure-Services. Azure AD biedt ondersteuning voor sterke verificatie-besturings elementen met meervoudige verificatie en krachtige methoden met een wacht woord.

Azure AD adviseert het volgende voor sterke verificatie-besturings elementen:

- Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg aanbevelingen voor identiteits-en toegangs beheer in Azure Security Center voor aanbevolen beveiligings procedures. Multi-factor Authentication afdwingen op alle, gebruikers of op per gebruikers niveau selecteren op basis van aanmeldings voorwaarden en risico factoren

- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar. Dit zijn onder andere, Windows hello voor bedrijven, Microsoft Authenticator-apps en on-premises verificatie methoden zoals Smart Cards

Zorg ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt voor beheerders en bevoegde gebruikers, gevolgd door een implementatie van een krachtig verificatie beleid voor andere gebruikers.

- [MFA inschakelen in P2S VPN voor virtuele WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in voor VPN-gebruikers (punt-naar-site) met behulp van Azure AD-verificatie. Multi-factor Authentication op basis van gebruikers configureren of gebruikmaken van multi-factor Authentication met voorwaardelijke toegang. Met voorwaardelijke toegang kunt u nauw keuriger bepalen hoe een tweede factor moet worden bevorderd. Hiermee kan multi-factor Authentication alleen worden toegewezen aan VPN en kunnen andere toepassingen worden uitgesloten die zijn gekoppeld aan de Azure AD-Tenant. 

Houd er rekening mee dat Azure AD-verificatie alleen beschikbaar is voor gateways met behulp van OpenVPN-protocol en clients met Windows.

- [Wat is voorwaardelijke toegang](../active-directory/conditional-access/overview.md)

- [Azure Active Directory-verificatie voor gebruikers-VPN configureren](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Chat-7: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: voor site-naar-site VPN in virtuele WAN wordt gebruikgemaakt van vooraf gedeelde sleutels (PSK) die worden gedetecteerd, gemaakt en beheerd door de klant in hun Azure Key Vault. Referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Voor GitHub kunt u de systeem eigen functie voor het scannen van geheime gegevens gebruiken om referenties of een andere vorm van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub Secret Scanning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="privileged-access"></a>Privileged Access

*Zie [Azure Security Bench Mark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)(Engelstalig) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Hulp**: Azure Virtual WAN maakt gebruik van Azure-functies voor toegangs beheer op basis van rollen (Azure RBAC) voor het isoleren van toegang tot bedrijfskritische systemen door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Beperk ook de toegang tot de beheer-, identiteits-en beveiligings systemen met beheerders toegang tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers, beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)voor meer informatie.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: gebruik punt-naar-site VPN, site-naar-site-VPN en versleutelde Express route met Virtual WAN voor uw connectiviteits vereisten. VPN-versleuteling beveiligt gegevens in transit van ' buiten-band-aanvallen (zoals verkeer vastleggen) om te voor komen dat aanvallers de gegevens kunnen lezen of wijzigen. 

- [Punt-naar-site-VPN](virtual-wan-point-to-site-portal.md)

- [Site-naar-site-VPN](virtual-wan-site-to-site-portal.md)

- [Versleutelde ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Bench Mark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: controleren of het beveiligings team inzicht heeft in Risico's voor assets

**Hulp**: Zorg ervoor dat beveiligings teams machtigingen voor beveiligings lezers krijgen in uw Azure-Tenant en-abonnementen zodat ze kunnen controleren op beveiligings Risico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Daarom moeten beveiligings inzichten en-risico's altijd centraal worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Opmerking: er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van de rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure Beheergroepen](../governance/management-groups/overview.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie. Azure Virtual WAN biedt ook ondersteuning voor op Azure Resource Manager gebaseerde implementaties van resources waarmee u Asset-sjablonen kunt exporteren. 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure monitor om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te geven om met Azure-resources te communiceren te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="logging-and-threat-detection"></a>Logboek registratie en detectie van bedreigingen

*Zie [Azure Security Bench Mark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)(Engelstalig) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Richt lijnen**: punt-naar-site-VPN met Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD). Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor-, Azure Sentinel-, SIEM-of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse-gebruiks voorbeelden. Deze zijn:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Gebruik Azure Security Center om waarschuwingen te maken voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen, waaronder afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kunt u met behulp van Security Center de module Threat Protection een uitgebreidere beveiligings waarschuwing verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL data base en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: detectie van bedreigingen inschakelen voor Azure-identiteits-en toegangs beheer

**Richt lijnen**: punt-naar-site-VPN met Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD). Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor-, Azure Sentinel-, SIEM-of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse-gebruiks voorbeelden. Deze zijn:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Gebruik Azure Security Center om waarschuwingen te maken voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen, waaronder afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kunt u met behulp van Security Center de module Threat Protection een uitgebreidere beveiligings waarschuwing verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL data base en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall configureren in een virtuele WAN-hub](howto-firewall.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Richt lijnen**: Bewaak Azure Virtual WAN met Azure monitor. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. Vermeldingen in het activiteiten logboek worden standaard verzameld en kunnen worden weer gegeven in de Azure Portal. U kunt Azure-activiteiten Logboeken (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement.

Er zijn ook verschillende Diagnostische logboeken beschikbaar voor virtuele WAN en kunnen worden geconfigureerd voor de virtuele WAN-resource met Azure Portal.  U kunt ervoor kiezen om te verzenden naar Log Analytics, streamen naar een Event Hub of eenvoudigweg te archiveren naar een opslag account. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

- [Azure Firewall-logboeken en metrische gegevens](/azure/firewall/logs-and-metrics)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: activiteiten logboeken van Azure, die automatisch worden ingeschakeld, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw virtuele WAN-resources van Azure, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden tijdens het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel Azure-resource Logboeken in voor virtuele WAN. U kunt Azure Security Center en Azure Policy gebruiken om resource logboeken en het verzamelen van logboek gegevens in te scha kelen. Deze logboeken kunnen essentieel zijn voor het later onderzoeken van beveiligings incidenten en het uitvoeren van forensische-oefeningen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Hulp** bij het inschakelen van beveiligings logboek registratie voor virtuele WAN met Azure monitor. Met Virtual WAN beschikt u over diverse netwerk-, beveiligings-en routerings functies om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven. Vermeldingen in het activiteiten logboek worden standaard verzameld en kunnen worden weer gegeven in de Azure Portal. U kunt Azure-activiteiten Logboeken (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement. 

Er zijn ook verschillende Diagnostische logboeken beschikbaar voor virtuele WAN en kunnen worden geconfigureerd voor de virtuele WAN-resource met Azure Portal. Verzenden naar Log Analytics, streamen naar een Event Hub of eenvoudigweg archiveren naar een opslag account. Daarnaast schakelt u gegevens in voor Azure Sentinel of een Security Information and Event Management-oplossing van derden. 
 

- [Virtuele WAN-logboeken en-metrische gegevens](logs-metrics.md)

- [Azure Firewall-logboeken en metrische gegevens](/azure/firewall/logs-and-metrics)

Azure Virtual WAN-beveiliging wordt via Azure Firewall verschaft. 

- [Documentatie over Azure Firewall](/azure/firewall/overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaar periode voor logboek opslag configureren

**Richt lijnen**: de Bewaar periode van het logboek configureren volgens uw naleving, regelgeving en zakelijke vereisten. In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center-waarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response)(Engelstalig) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: voor bereiding-respons proces van incidenten bijwerken voor Azure

**Hulp**: Zorg ervoor dat uw organisatie processen heeft om te reageren op beveiligings incidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regel matig de voor bereidingen spelen.

- [Implementeer beveiliging in de bedrijfs omgeving](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslag Gids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: voor bereiding-incident melding instellen

**Richt lijnen**: contact gegevens voor beveiligings incidenten instellen in azure Security Center. Deze contact gegevens worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. U hebt ook opties voor het aanpassen van incident waarschuwingen en meldingen in verschillende Azure-Services op basis van de behoeften van uw incident respons. 

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u lessen uit eerdere incidenten ontdekken en waarschuwingen voor analisten prioriteiten geven, zodat ze geen tijd verspillen op valse positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC data connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. Met Azure Sentinel kunt u geavanceerde waarschuwings regels maken om incidenten automatisch te genereren voor een onderzoek. 

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de export functie om Risico's voor Azure-resources te identificeren. Waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.

- [Exporteren configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detectie en analyse: een incident onderzoeken

**Richt lijnen**: Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken bij het onderzoeken van mogelijke incidenten, om een volledig overzicht te maken van wat er is gebeurd. Diverse logboeken moeten worden verzameld om de activiteiten van een mogelijke aanvaller in de Kill-keten te volgen om blinde vlekken te voor komen.  U moet er ook voor zorgen dat inzichten en informatie worden vastgelegd voor andere analisten en voor toekomstige historische Naslag informatie.  

De gegevens bronnen voor onderzoek bevatten de gecentraliseerde logboek registratie bronnen die al worden verzameld van de services binnen het bereik en het uitvoeren van systemen, maar kan ook het volgende omvatten:

- Netwerk gegevens: gebruik stroom logboeken voor netwerk beveiligings groepen, Azure Network Watcher en Azure Monitor om netwerk stroom logboeken en andere analyse-informatie vast te leggen. 

- Moment opnamen van actieve systemen: 

    - Gebruik de functie voor moment opnamen van de virtuele machine van Azure om een moment opname te maken van de schijf waarop het systeem wordt uitgevoerd. 

    - Gebruik de systeem eigen geheugen dump functie van het besturings systeem om een moment opname te maken van het actieve systeem geheugen.

    - Gebruik de functie snap shot van de Azure-Services of de eigen mogelijkheid van uw software om moment opnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide gegevens analyse voor vrijwel elke logboek bron en een case beheer Portal om de volledige levens duur van incidenten te beheren. Informatie over Intelligence tijdens een onderzoek kan worden gekoppeld aan een incident voor het bijhouden en rapporteren van de doel einden. 

- [Moment opname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Moment opname maken van de schijf van een Linux-machine](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure ondersteuning voor diagnostische gegevens en geheugen dump verzameling](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detectie en analyse: de prioriteit van incidenten bepalen

**Richt lijnen**: Geef een context voor analisten waarop incidenten zich richten op basis van ernst van de waarschuwing en de gevoeligheid van een activa. 

Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevings systeem maken om Azure-resources te identificeren en categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: insluiting, uitroeiing en herstel: de verwerking van incidenten automatiseren

**Richt lijnen**: Automatiseer hand matige terugkerende taken om de reactie tijd te versnellen en de overhead voor analisten te verlagen. Het uitvoeren van hand matige taken duurt langer om uit te voeren, elk incident te vertragen en te verminderen hoeveel incidenten een analist kan verwerken. Hand matige taken verg Roten ook analisten intensief, waardoor het risico van menselijke fout wordt verg root dat vertragingen veroorzaakt, en de mogelijkheid van analisten om effectief te best Eden aan complexe taken te degraderen. Gebruik werk stroom automatiserings functies in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een Playbook uit te voeren om te reageren op binnenkomende beveiligings waarschuwingen. De Playbook neemt acties, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werk stroom automatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Automatische reacties op dreigingen instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatische bedreigings reacties instellen in azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="posture-and-vulnerability-management"></a>Postuur en beveiligings beheer

*Zie voor meer informatie de [Azure Security Bench Mark: postuur en het beveiligings beleid](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: regel matige aanvals simulatie uitvoeren

**Richt lijnen**: u kunt zo nodig indringings tests of rode team activiteiten uitvoeren op uw Azure-resources en ervoor zorgen dat alle essentiële beveiligings resultaten worden hersteld.
Volg de Microsoft Cloud regels voor het testen van de indringings fase om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Indringings tests in azure](../security/fundamentals/pen-testing.md)

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="endpoint-security"></a>Endpoint Security

*Zie [Azure Security Bench Mark: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)(Engelstalig) voor meer informatie.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: eindpunt detectie en-antwoord gebruiken (EDR)

**Richt lijnen**: klanten mogen geen instellingen voor het configureren van het eind punt detecteren en antwoorden. De Virtual Machines die in de Azure Virtual WAN-aanbieding wordt gebruikt, maken echter gebruik van deze mogelijkheden. Meer informatie over deze algemene mogelijkheden vindt u op de koppelingen waarnaar wordt verwezen. 

- [Overzicht van micro soft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Micro soft Defender ATP-service voor Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Micro soft Defender ATP-service voor niet-Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Bench Mark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy)voor meer informatie.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: de strategie voor Asset Management en gegevens bescherming definiëren 

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Volg prioriteiten voor detectie, beoordeling, beveiliging en bewaking van bedrijfs kritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard gegevens classificatie in overeenstemming met de zakelijke Risico's

-   Zicht baarheid van beveiligings organisaties in Risico's en activa-inventaris 

-   Goed keuring van beveiligings organisaties van Azure-Services voor gebruik 

-   Beveiliging van assets via hun levens cyclus

-   Vereiste strategie voor toegangs beheer in overeenstemming met organisatie gegevens classificatie

-   Gebruik van functies voor gegevens bescherming van Azure native en derden

-   Gegevens versleutelings vereisten voor in-transit-en rest-use cases

-   Juiste cryptografische normen

Zie voor meer informatie de volgende verwijzingen:
- [Aanbeveling van Azure-beveiligings architectuur-opslag, gegevens en versleuteling](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Basis beginselen van Azure-beveiliging-Azure-gegevens beveiliging,-versleuteling en-opslag](../security/fundamentals/encryption-overview.md)

- [Cloud-acceptatie Framework-aanbevolen procedures voor Azure Data Security en versleuteling](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Bench Mark-Asset Management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Bench Mark-gegevens beveiliging](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: ondernemings segmentatie strategie definiëren 

**Richt lijnen**: Stel een bedrijfs strategie in om de toegang tot assets te segmenteren met een combi natie van identiteits-, netwerk-, toepassings-, abonnements-, beheer groep-en andere besturings elementen.

Houd de nood zaak van beveiligings scheiding zorgvuldig bij met de nood zaak om de dagelijkse werking van de systemen in te scha kelen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatie strategie consistent wordt geïmplementeerd in alle controle typen, zoals netwerk beveiliging, identiteits-en toegangs modellen, en toepassings machtigingen/toegangs modellen en besturings elementen voor menselijke processen.

- [Richt lijnen voor segmentatie strategie in azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richt lijnen voor segmentatie strategie in azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerk segmentatie uitlijnen met strategie voor bedrijfs segmentatie](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: beveiligings strategie voor postuur-beheer definiëren

**Hulp**: regel matig de Risico's voor uw afzonderlijke assets en de omgeving waarin ze worden gehost. Volg prioriteiten voor hoogwaardige assets en zeer belichte aanvallen, zoals gepubliceerde toepassingen, netwerk binnenkomend en uitgevende punten, gebruikers-en beheerders eindpunten, enzovoort.

- [Azure Security Bench Mark-postuur en beveiligings beheer](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: organisatie rollen, verantwoordelijkheden en accountabilities uitlijnen

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Volg prioriteiten voor een duidelijke verantwoordelijkheid voor beveiligings beslissingen, het trainen van iedereen op het gedeelde verantwoordelijkheids model en technische teams op technologie om de cloud te beveiligen.

- [Best Practice voor Azure-beveiliging 1: personen: teams trainen in Cloud Security traject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Aanbevolen beveiligings procedure voor Azure-gebruikers: teams in de Cloud-beveiligings technologie trainen](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Best Practice voor Azure-beveiliging 3-proces: verantwoordelijkheid toewijzen voor Cloud beveiligings beslissingen](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: netwerk beveiligings strategie definiëren

**Richt lijnen**: Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerk beheer en beveiligings verantwoordelijkheid

-   Segment model voor virtuele netwerken dat is afgestemd op de strategie voor bedrijfs segmentatie

-   Herstel strategie in verschillende scenario's voor bedreigingen en aanvallen

-   Internet-en ingangs-en uitgangs strategie

-   Hybride Cloud en on-premises interconnectiviteit-strategie

-   Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Zie voor meer informatie de volgende verwijzingen:
- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Bench Mark-netwerk beveiliging](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Overzicht van Azure-netwerk beveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor bedrijfs netwerk architectuur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identiteits-en bevoorrechte toegangs strategie definiëren

**Richt lijnen**: Stel een Azure Identity and privileged Access benaderingen in als onderdeel van de algehele strategie voor beveiligings beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits-en verificatie systeem en de interconnectiviteit daarvan met andere interne en externe identiteits systemen

-   Sterke authenticatie methoden in verschillende use-cases en-voor waarden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikers activiteiten  

-   Beoordelings-en afstemmings proces voor gebruikers-id en-toegang

Zie voor meer informatie de volgende verwijzingen:

- [Azure Security Bench Mark-Identity Management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security-benchmark-privileged Access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van Azure Identity Management-beveiliging](../security/fundamentals/identity-management-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: vastleggen van de logboek registratie en de reactie op bedreigingen

**Richt lijnen**: Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen terwijl aan nalevings vereisten wordt voldaan. Volg prioriteiten om analisten te voorzien van waarschuwingen met hoge kwaliteit en naadloze ervaring, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps) 

-   Een goed gedefinieerd respons proces voor incidenten dat wordt afgestemd op het NIST of een ander branche raamwerk 

-   Vastleggen en bewaren in Logboeken voor ondersteuning van detectie van bedreigingen, reacties op incidenten en nalevings behoeften

-   Gecentraliseerde zicht baarheid van en correlatie-informatie over bedreigingen, met behulp van SIEM, systeem eigen Azure-mogelijkheden en andere bronnen 

-   Communicatie-en meldings abonnement met uw klanten, leveranciers en open bare partijen

-   Gebruik van Azure native en platforms van derden voor het verwerken van incidenten, zoals logboek registratie en detectie van bedreigingen, forensische, herstel en uitroeiing van aanvallen

-   Processen voor het verwerken van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijs behoud

Zie voor meer informatie de volgende verwijzingen:

- [Azure Security Bench Mark-logboek registratie en detectie van bedreigingen](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Bench Mark-incident respons](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Aanbevolen procedure voor Azure-beveiliging 4-proces. Reactie processen voor incidenten bijwerken voor Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Hand leiding Azure-acceptatie raamwerk, logboek registratie en rapportage](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaal, beheer en bewaking van Azure Enter prise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)
