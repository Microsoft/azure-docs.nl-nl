---
title: Azure-beveiligings basislijn voor Windows virtueel bureau blad
description: De Windows-basis lijn voor Virtual Desktop beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42795e2dda6df24e656c9c06f6a9424bd9e4b5cb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092979"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure-beveiligings basislijn voor Windows virtueel bureau blad

Deze beveiligings basislijn is van toepassing op de richt lijnen van [Azure Security Bench Mark versie 2,0](../security/benchmarks/overview.md) naar Windows Virtual Desktop. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op virtuele Windows-Bureau bladen. **Besturings elementen** die niet van toepassing zijn op virtuele Windows-Bureau bladen, zijn uitgesloten.

De virtueel-bureaublad service van Windows omvat de service zelf, de virtuele SKU Windows 10 Enter prise voor meerdere sessies en FSLogix. Zie de [beveiligings basislijn voor opslag](../storage/common/security-baseline.md)voor FSLogix beveiligings aanbevelingen. De service heeft een agent die wordt uitgevoerd op virtuele machines, maar omdat de virtuele machines volledig worden beheerd door de klant, volgt u de [beveiligings aanbevelingen voor Compute](../virtual-machines/windows/security-baseline.md)

Voor een overzicht van de manier waarop Windows virtueel bureau blad volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Windows-bureau blad voor beveiliging basislijn toewijzing](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Richt lijnen**: u moet een bestaand virtueel netwerk maken of gebruiken wanneer u virtuele machines implementeert die moeten worden geregistreerd voor het virtuele bureau blad van Windows. Zorg ervoor dat alle virtuele netwerken van Azure een bedrijfs segmentatie principe volgen die afstemt op de bedrijfs Risico's. Elk systeem dat een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtueel netwerk en voldoende is beveiligd met een netwerk beveiligings groep of Azure Firewall.

Gebruik adaptieve functies voor het beveiligen van netwerken in Azure Security Center om configuraties voor netwerk beveiligings groepen aan te bevelen die poorten en bron-Ip's beperken met verwijzing naar regels voor extern netwerk verkeer.

Op basis van uw toepassingen en de segmentatie strategie van een onderneming kunt u verkeer beperken of toestaan tussen interne bronnen op basis van de regels voor de netwerk beveiligings groep. Voor specifieke, goed gedefinieerde toepassingen (zoals een app met drie lagen) kan dit een zeer veilige ' weigeren standaard, met uitzonde ring toestaan ' zijn. Dit kan niet goed worden geschaald als er veel toepassingen en eind punten met elkaar communiceren. U kunt Azure Firewall ook gebruiken in omstandigheden waarbij centraal beheer is vereist voor een groot aantal ondernemings segmenten of spokes (in een hub/spoke-topologie)

Voor de netwerk beveiligings groepen die zijn gekoppeld aan uw virtuele machine (die deel uitmaken van Windows virtueel bureau blad) subnetten, moet u uitgaand verkeer naar specifieke eind punten toestaan. 

- [Meer informatie over welke Url's moeten worden toegestaan voor toegang tot virtueel bureau blad van Windows](safe-url-list.md)

- [Adaptieve netwerk beveiliging in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall voor virtueel bureau blad van Windows ](../firewall/protect-windows-virtual-desktop.md)

- [Een netwerk beveiligings groep met beveiligings regels maken](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Richt lijnen**: gebruik Azure ExpressRoute of een virtueel particulier Azure-netwerk om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet, bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. 

Voor punt-naar-site-en site-naar-site-VPN-netwerken kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van opties voor virtueel particulier netwerk en Azure ExpressRoute.

Gebruik peering op virtueel netwerk om twee of meer virtuele netwerken in azure met elkaar te verbinden. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en blijft in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../expressroute/expressroute-connectivity-models.md) 

- [Overzicht van Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering op virtueel netwerk](/azure/virtual-network/virtual-network-peering-overview)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Hulp**: gebruik Azure firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. Beveilig uw virtuele bureau blad-resources tegen aanvallen van externe netwerken, inclusief gedistribueerde Denial of service-aanvallen, toepassingsspecifieke aanvallen, ongevraagd en mogelijk schadelijk Internet verkeer. Bescherm uw assets tegen gedistribueerde Denial of service-aanvallen door DDoS Standard-beveiliging in te scha kelen op uw Azure Virtual Networks. Gebruik Azure Security Center om onjuiste configuratie Risico's met betrekking tot uw netwerk bronnen te detecteren.

Virtueel bureau blad van Windows is niet bedoeld voor het uitvoeren van webtoepassingen. u hoeft geen extra instellingen te configureren of extra netwerk services te implementeren om deze te beveiligen tegen aanvallen van buitenaf in externe netwerken.

- [Documentatie over Azure Firewall](/azure/firewall)

- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: gebruik Azure Firewall met op bedreigingen gebaseerd filteren om te waarschuwen en eventueel verkeer van en naar bekende schadelijke IP-adressen en domeinen te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. Wanneer Payload-inspectie vereist is, kunt u een externe detectie-of preventie oplossing van derden implementeren vanuit Azure Marketplace. 

Als u een wettelijke of andere vereiste hebt voor het gebruik van inbraak detectie of preventie van voor komen, moet u ervoor zorgen dat deze altijd is afgestemd op waarschuwingen van hoge kwaliteit voor uw SIEM-oplossing (Security Information and Event Management).

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace bevat mogelijkheden voor externe ID'S van derden](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Micro soft Defender ATP EDR-mogelijkheid](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: Azure Virtual Network Service-Tags gebruiken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of een Azure firewall die is geconfigureerd voor uw Windows-bureau blad-resources. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld: WindowsVirtualDesktop) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Klik hier voor meer informatie over wat wordt beschreven in het label Windows Virtual Desktop service ](safe-url-list.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Hulp**: virtueel bureau blad van Windows maakt gebruik van Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de cloudbeveiligingsprocedure van uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen beoordelen in hoeverre uw identiteitsbeveiliging voldoet aan de aanbevelingen op basis van best practices van Microsoft. Gebruik de score om te meten hoe nauwkeurig uw configuratie overeenkomt met aanbevelingen op basis van best practices, en om verbeteringen aan te brengen in uw beveiligingsaanpak.

Azure AD biedt ondersteuning voor externe identiteiten waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit.

- [Tenancy in Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Externe ID-providers voor een toepassing gebruiken](/azure/active-directory/b2b/identity-providers)

- [Wat is de id-beveiligingsscore in Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

- [Specifieke rollen die u nodig hebt om virtuele Windows-Bureau bladen te kunnen uitvoeren ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Hulp**: virtueel bureau blad van Windows ondersteunt door Azure beheerde identiteiten voor niet-humane accounts, zoals services of automatisering. Het wordt aanbevolen Azure Managed Identity te gebruiken in plaats van een krachtig menselijk account te maken om uw resources te openen of uit te voeren. 

Virtueel bureau blad van Windows raadt u aan om met behulp van Azure Active Directory (Azure AD) een service-principal te maken met beperkte machtigingen op resource niveau om service-principals met certificaat referenties te configureren en terug te vallen op client geheimen. In beide gevallen kunnen Azure Key Vault worden gebruikt in combi natie met door Azure beheerde identiteiten, zodat de runtime-omgeving (zoals een Azure function) de referentie kan ophalen uit de sleutel kluis.

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps) 

- [Een service-principal maken met certificaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Azure Key Vault gebruiken voor registratie van beveiligings-principal](../key-vault/general/authentication.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: virtueel bureau blad van Windows maakt gebruik van Azure Active Directory (Azure AD) om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze beveiligde toegang met meer zicht baarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang 

**Hulp**: virtuele bureau blad van Windows maakt gebruik van Azure Active Directory (Azure AD), dat ondersteuning biedt voor krachtige verificatie controles door meervoudige verificatie en methoden met een sterke wacht woord.

- Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg aanbevelingen voor identiteits-en toegangs beheer van Azure Security Center voor een aantal aanbevolen procedures in de configuratie van multi-factor Authentication. Multi-factor Authentication kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.

- Verificatie zonder wachtwoord: er zijn drie verificatieopties zonder een wachtwoord beschikbaar: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Virtueel bureau blad van Windows ondersteunt verouderde verificatie op basis van wacht woorden, zoals alleen-Cloud accounts (gebruikers accounts die rechtstreeks in azure zijn gemaakt) die een basislijn wachtwoord beleid of Hybrid accounts (gebruikers accounts uit on-premises Azure AD volgen die de on-premises wachtwoord beleidsregels nagaan). Bij het gebruik van verificatie op basis van wacht woorden biedt Azure AD een functie voor wachtwoord beveiliging waarmee wordt voor komen dat gebruikers wacht woorden kunnen opgeven die gemakkelijk te raden zijn. Micro soft biedt een algemene lijst met verboden wacht woorden die worden bijgewerkt op basis van de telemetrie en klanten kunnen de lijst uitbreiden op basis van hun behoeften (zoals een huis stijl, culturele verwijzingen, enzovoort). Deze wachtwoord beveiliging kan worden gebruikt voor Cloud-en hybride accounts.

Verificatie op basis van wachtwoord referenties is alleen vatbaar voor populaire aanvals methoden. Voor een betere beveiliging gebruikt u sterke verificatie zoals multi-factor Authentication en een krachtig wachtwoord beleid. Voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen hebben, moet u deze wijzigen bij de eerste installatie van de service.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van sterke authenticatie methoden wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie door te rollen naar andere gebruikers.

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Verwijder ongeldige wacht woorden met Azure Active Directory wachtwoord beveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: virtueel bureau blad van Windows is geïntegreerd met Azure Active Directory (Azure AD), dat de volgende gegevens bronnen biedt:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of een SIEM-systemen (Security Information and Event Management) van derden. Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Rapporten van activiteiten controleren in azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../security-center/alerts-reference.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Hulp**: virtueel bureau blad van Windows biedt ondersteuning voor voorwaardelijke toegang met Azure Active Directory (Azure AD) voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden. Gebruikers aanmeldingen van bepaalde IP-bereiken kunnen bijvoorbeeld worden vereist voor het gebruik van multi-factor Authentication voor toegang. 

Daarnaast kan een gedetailleerd beheer beleid voor verificatie sessies ook worden gebruikt voor verschillende use cases.

- [Overzicht van voorwaardelijke toegang in Azure](../active-directory/conditional-access/overview.md) 

- [Veelvoorkomend beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Het beheer van verificatiesessies via voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [De Windows-specifieke instellingen voor de voorwaardelijke toegang van virtuele Bureau bladen vindt u hier](set-up-mfa.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](/azure/security/benchmarks/security-controls-v2-privileged-access) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Hulp**: Windows Virtual Desktop maakt gebruik van Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor het isoleren van toegang tot bedrijfs kritieke systemen. Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers, beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op bedrijfs kritieke systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Mini maal benodigde beheerders machtigingen voor het beheren van virtuele Windows-Bureau bladen](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Richt lijnen**: in Windows Virtual Desktop worden Azure Active Directory-accounts (Azure AD) gebruikt om de resources te beheren, gebruikers accounts en toegangs toewijzing regel matig te controleren om ervoor te zorgen dat de accounts en hun toegang geldig zijn.

Gebruik Azure AD-toegangs beoordelingen om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Ingebouwde rollen voor Windows virtueel bureau blad](rbac.md)

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Hulp**: virtuele bureau blad van Windows maakt gebruik van Azure Active Directory (Azure AD) voor het beheren van de bijbehorende resources. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor noodtoegang hebben meestal zeer uitgebreide bevoegdheden en kunnen beter niet aan specifieke personen worden toegewezen. Accounts voor noodtoegang zijn alleen bestemd voor echte noodsituaties waarin normale beheerdersaccounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wachtwoord, certificaat of smartcard) voor accounts voor noodtoegang veilig worden bewaard en alleen bekend zijn bij personen die deze alleen in een noodgeval mogen gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: virtueel bureau blad van Windows is geïntegreerd met Azure Active Directory (Azure AD) voor het beheren van de bijbehorende resources. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verlopen. In aanvullende, twee of meerdere fase goedkeuringen worden ook ondersteund.

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md) 

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richt lijnen**: beveiligde en geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen, zoals beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken. 

Gebruik Azure Active Directory (Azure AD), micro soft Defender Advanced Threat Protection (ATP) of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. Het beveiligde werk station kan centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Een werkstation met uitgebreide toegang gebruiken](/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: virtueel bureau blad van Windows is geïntegreerd met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. 

De bevoegdheden die u toewijst aan resources met Azure RBAC, moeten altijd beperkt zijn tot de machtigingen die nodig zijn voor de rollen. Dit is een aanvulling op de just-in-time (JIT) benadering van Privileged Identity Management (PIM), met Azure Active Directory (Azure AD) en moet regel matig worden gecontroleerd.

Daarnaast kunt u ingebouwde rollen gebruiken om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md) 

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Ingebouwde rollen voor Windows virtueel bureau blad](rbac.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning  

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang heeft tot klant gegevens, ondersteunt Windows virtueel bureau blad klanten-lockbox om een interface te bieden waarmee u aanvragen voor toegang tot klant gegevens kunt controleren en goed keuren of afwijzen.

- [Klanten-lockbox begrijpen](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](/azure/security/benchmarks/security-controls-v2-data-protection) voor meer informatie.*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Detectie, classificatie en labeling van gevoelige gegevens

**Richt lijnen**: uw gevoelige gegevens detecteren, classificeren en labelen zodat u de juiste besturings elementen kunt ontwerpen. Dit is om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologie systemen van de organisatie.

Gebruik Azure Information Protection (en de bijbehorende Scan Tool) voor gevoelige informatie in Office-documenten op Azure, on-premises, Office 365 en andere locaties.

U kunt Azure SQL Information Protection gebruiken bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-databases.

- [Gevoelige gegevens labelen met Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Azure SQL-gegevensdetectie implementeren](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

**Richt lijnen**: Beveilig gevoelige gegevens door de toegang te beperken met behulp van Azure-rol op basis van Access Control (Azure RBAC), op het netwerk gebaseerde toegangs beheer functies en specifieke besturings elementen in Azure-Services (zoals VERSLEUTELING in SQL en andere data bases).

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Micro soft behandelt alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren op niet-geautoriseerde overdracht van gevoelige gegevens

**Richtlijnen**: Controleer op niet-geautoriseerde overdracht van gegevens naar locaties buiten de zichtbaarheid en controle van de onderneming. Het gaat hier dan meestal om het controleren op afwijkende activiteiten (grote of ongebruikelijke overdrachten) die kunnen wijzen op niet-geautoriseerde gegevensexfiltratie.

De functies van Advanced Threat Protection (ATP) met zowel Azure Storage als Azure SQL-ATP kunnen een waarschuwing geven over afwijkende overdracht van informatie, hetgeen aangeeft wat ongeoorloofde overdracht van gevoelige gegevens is.

Azure Information Protection (AIP) biedt controlevoorzieningen voor informatie die is geclassificeerd en gelabeld.

Gebruik oplossingen voor preventie van gegevens verlies, zoals de hosts, om detectie en/of preventieve controles af te dwingen om gegevens exfiltration te voor komen.

- [Azure SQL ATP inschakelen](../azure-sql/database/threat-detection-overview.md) 

- [Azure Storage ATP inschakelen](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](/azure/security/benchmarks/security-controls-v2-asset-management) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist voor de zicht baarheid van werk belastingen en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Gebruik Azure virtual machine Inventory om het verzamelen van informatie over software op Virtual Machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u toegang wilt krijgen tot installatie datum en andere informatie, schakelt u Diagnostische gegevens op gast niveau in en brengt u de Windows-gebeurtenis Logboeken in een Log Analytics-werk ruimte.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Hulp**: gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richt lijnen**: niet van toepassing. Virtueel bureau blad van Windows kan niet worden gebruikt om de beveiliging van assets in een levenscyclus beheer proces te garanderen. Het is de verantwoordelijkheid van de klant om kenmerken en netwerk configuraties van activa te onderhouden die als hoge impact worden beschouwd. 

Het is raadzaam dat de klant een proces maakt om het kenmerk en de wijzigingen in de netwerk configuratie vast te leggen, de wijzigings invloed te meten en herstel taken te maken, indien van toepassing.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: alleen goedgekeurde toepassingen in reken bronnen gebruiken

**Hulp**: Azure virtual machine Inventory gebruiken om het verzamelen van informatie over alle software op virtuele machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u toegang wilt krijgen tot installatie datum en andere informatie, schakelt u Diagnostische gegevens op gast niveau in en brengt u de Windows-gebeurtenis Logboeken in een Log Analytics-werk ruimte.

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Hulp**: gebruik de Azure Security Center ingebouwde functie voor detectie van bedreigingen en schakel Azure Defender (formeel Azure Advanced Threat Protection) in voor uw virtuele bureau blad-resources van Windows. Azure Defender voor Windows Virtual Desktop biedt een extra beveiligingslaag voor ongebruikelijke en mogelijk schadelijke pogingen om uw Windows-bureau blad-resources te openen of misbruik te maken.

Door sturen van alle logboeken van virtueel bureau blad van Windows naar uw SIEM-oplossing (Security Information Event Management) die kan worden gebruikt om aangepaste bedreigings detecties in te stellen. Zorg ervoor dat u verschillende typen Azure-assets bewaakt voor mogelijke dreigingen en afwijkingen. Richt u op het ophalen van waarschuwingen met hoge kwaliteit om te voor komen dat de fout-positieven worden gereduceerd. Waarschuwingen kunnen worden gebrond op basis van logboek gegevens, agents of andere gegevens.

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection) 

- [Naslag Gids voor beveiligings waarschuwingen Azure Security Center](../security-center/alerts-reference.md)

- [Aangepaste analyse regels maken voor het detecteren van bedreigingen](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyber Threat Intelligence met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

**Hulp**: Azure Active Directory (Azure AD) biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel of andere beveiligings informatie en Siem (Security Information and Event Management) of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse-gebruiks voorbeelden:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kunt ook waarschuwen voor bepaalde verdachte activiteiten, zoals overmatig aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kan de module bedreigings beveiliging in Azure Security Center ook uitgebreidere beveiligings waarschuwingen verzamelen van afzonderlijke Azure-reken resources (virtuele machines, containers, app service), gegevens bronnen (SQL DB en opslag) en Azure-service lagen. Met deze mogelijkheid kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

**Hulp**: Windows Virtual Desktop produceert of verwerkt geen DNS-query Logboeken (Domain Name Service). Resources die zijn geregistreerd voor de service, kunnen echter stroom logboeken produceren.

De resource-en stroom logboeken van de netwerk beveiligings groep inschakelen en verzamelen, Azure Firewall logboeken en WAF-Logboeken (Web Application firewall) voor beveiligings analyse ter ondersteuning van incident onderzoek, de jacht van dreigingen en het genereren van beveiligings waarschuwingen. U kunt de stroom logboeken naar een Azure Monitor Log Analytics werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzichten te geven.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall-logboeken en metrische gegevens](/azure/firewall/logs-and-metrics) 

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md) 

- [Azure-netwerk bewakings oplossingen in Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch worden ingeschakeld, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw virtuele bureau blad-resources van Windows, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, de hulpprogram ma's die worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor het bewaren van gegevens.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM (Security Information Event Management) van derden. Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response) voor meer informatie.*

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

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

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

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

**Hulp**: gebruik Azure Security Center en Azure Policy om veilige configuraties te maken voor alle reken resources, waaronder vm's, containers en anderen.

U kunt aangepaste installatie kopieën van besturings systemen of Azure Automation status configuratie gebruiken om de beveiligings configuratie te bepalen van het besturings systeem dat door uw organisatie wordt vereist.

- [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md) 

- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md) 

- [Windows Virtual Desktop-omgeving](environment-setup.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: beveiligde configuraties voor reken bronnen

**Hulp**: gebruik Azure Security Center en Azure Policy om regel matig configuratie Risico's te beoordelen en te herstellen voor uw Azure-reken resources, waaronder virtuele machines, containers en anderen. Daarnaast kunt u Azure Resource Manager-sjablonen, aangepaste installatie kopieën van besturings systemen of de configuratie van Azure Automation status gebruiken om de beveiligings configuratie van het besturings systeem te onderhouden dat door uw organisatie wordt vereist. De micro soft-sjablonen voor virtuele machines in combi natie met de configuratie van de Azure Automation status kunnen helpen bij de vergadering en het onderhouden van de beveiligings vereisten.

Azure Marketplace-installatie kopieën voor virtuele machines die door micro soft zijn gepubliceerd, worden beheerd en onderhouden door micro soft.

Azure Security Center kunt ook beveiligings problemen in container installatie kopie scannen en doorlopende bewaking uitvoeren van uw docker-configuratie in containers op basis van de docker-Bench Mark van het Internet beveiligings centrum. U kunt de pagina aanbevelingen voor Azure Security Center gebruiken om aanbevelingen te bekijken en problemen op te lossen.

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Een virtuele Azure-machine maken op basis van een ARM-sjabloon](../virtual-machines/windows/ps-template.md) 

- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md) 

- [Containerbeveiliging in Security Center](../security-center/container-security.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>HW-5: Sla het aangepaste besturings systeem en de container installatie kopieën veilig op

**Hulp**: met Windows Virtual Desktop kunnen klanten installatie kopieën van besturings systemen beheren. Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot uw aangepaste installatie kopieën. Een galerie met gedeelde Azure-afbeeldingen gebruiken u kunt uw installatie kopieën delen met verschillende gebruikers, service-principals of Active Directory groepen binnen uw organisatie. Container installatie kopieën opslaan in Azure Container Registry en RBAC gebruiken om ervoor te zorgen dat alleen gemachtigde gebruikers toegang hebben.

- [Meer informatie over Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Overzicht van Galerie gedeelde afbeeldingen](/azure/virtual-machines/windows/shared-image-galleries)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: software-evaluatie van beveiligings problemen uitvoeren

**Richt lijnen**: met virtuele Bureau bladen van Windows kunt u uw eigen virtuele machines implementeren en deze registreren bij de service, en SQL database in de omgeving worden uitgevoerd.

Windows Virtual Desktop kan gebruikmaken van een oplossing van derden voor het uitvoeren van evaluatie van beveiligings problemen op netwerk apparaten en webtoepassingen. Gebruik bij het uitvoeren van externe scans geen enkele, permanente, beheerders account. Overweeg de implementatie van JIT-inrichtings methodologie voor het account van de scan. Referenties voor het Scan account moeten worden beveiligd, gecontroleerd en alleen worden gebruikt voor het scannen van beveiligings problemen.

Zo nodig worden scan resultaten met consistente intervallen uitgevoerd en wordt de resultaten vergeleken met eerdere scans om te controleren of beveiligings problemen zijn opgelost.

Volg de aanbevelingen van Azure Security Center voor het uitvoeren van evaluatie van beveiligings problemen op uw Azure virtual machines (en SQL-servers). Azure Security Center heeft een ingebouwde beveiligings scanner voor de virtuele machine, container installatie kopieën en SQL database.

Zoals vereist, worden scan resultaten consistent door lopen en worden de resultaten vergeleken met de vorige scans om te controleren of beveiligings problemen zijn opgelost. Wanneer u aanbevelingen voor beveiligings beheer gebruikt die worden voorgesteld door Azure Security Center, kunt u in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Geïntegreerde scanner voor beveiligings problemen voor virtuele machines](/azure/security-center/built-in-vulnerability-assessment) 
- [Evaluatie van SQL-beveiligings problemen](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Beveiligingsproblemen in software snel en automatisch oplossen

**Hulp**: Windows Virtual Desktop gebruikt of vereist geen software van derden. Met Windows virtueel bureau blad kunt u echter uw eigen virtuele machines implementeren en registreren bij de service.

Gebruik Azure Automation Updatebeheer of een oplossing van derden om ervoor te zorgen dat de meest recente beveiligings updates zijn geïnstalleerd op uw virtuele Windows Server-machines. Zorg ervoor dat Windows Update is ingeschakeld en is ingesteld om automatisch te worden bijgewerkt voor virtuele Windows-machines.

Gebruik een patch beheer oplossing van derden voor software van derden of System Center Updates Publisher voor Configuration Manager.

- [Updatebeheer configureren voor virtuele machines in azure](/azure/automation/update-management/overview) 

- [Updates en patches voor uw Azure-VM's beheren](/azure/automation/update-management/manage-updates-for-vm)

- [Micro soft endpoint Configuration Manager configureren voor virtueel bureau blad van Windows](configure-automatic-updates.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Hulp**: met Windows Virtual Desktop kunnen klanten geen eigen indringings tests uitvoeren op hun virtuele bureau blad-resources van Windows.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="endpoint-security"></a>Endpoint Security

*Zie [Azure Security Bench Mark: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)(Engelstalig) voor meer informatie.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: eindpunt detectie en-antwoord gebruiken (EDR)

**Richt lijnen**: Windows virtueel bureau blad biedt geen specifieke mogelijkheden voor processen voor het detecteren en verwerken van eind punten. Resources die bij de service zijn geregistreerd, kunnen profiteren van de eindpunt detectie en reactie mogelijkheden. 

Schakel eindpunt detectie en respons mogelijkheden voor servers en clients in en integreer ze met SIEM-oplossingen (Security Information and Event Management) en beveiligings bewerkingen.

Geavanceerde beveiliging tegen bedreigingen van micro soft Defender biedt eindpunt detectie-en reactie mogelijkheden als onderdeel van een Enter prise endpoint-beveiligings platform om geavanceerde bedreigingen te voor komen, te detecteren, te onderzoeken en te reageren.

- [Overzicht van micro soft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Micro soft Defender ATP-service voor Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Micro soft Defender ATP-service voor niet-Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Micro soft Defender ATP voor niet-permanente virtuele-bureaublad infrastructuur](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: centraal beheerde moderne anti-malware-software gebruiken

**Hulp**: Beveilig uw virtuele bureau blad-resources met een centraal beheerde en moderne anti-malware-oplossing die in realtime en periodiek scannen kan worden uitgevoerd.

Azure Security Center kunt het gebruik van een aantal populaire oplossingen voor anti-malware voor uw virtuele machines automatisch identificeren en de status van de Endpoint Protection-uitvoering melden en aanbevelingen doen.

Micro soft antimalware voor Azure Cloud Services is de standaard anti-malware voor virtuele Windows-machines (Vm's). U kunt ook detectie van bedreigingen met Azure Security Center voor gegevens Services gebruiken om malware te detecteren die is geüpload naar Azure Storage accounts.

- [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md) 

- [Ondersteunde Endpoint Protection-oplossingen](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: Zorg ervoor dat anti-malware-hand tekeningen snel en consistent worden bijgewerkt.

Volg de aanbevelingen in Azure Security Center: "COMPUTE &amp; apps" om ervoor te zorgen dat alle virtuele machines en/of containers up-to-date zijn met de nieuwste hand tekeningen.

Micro soft antimalware installeert standaard automatisch de nieuwste hand tekeningen en engine-updates.

- [Micro soft antimalware implementeren voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md) 

- [Endpoint Protection-evaluatie en aanbevelingen in Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Benchmark: back-up en herstel](/azure/security/benchmarks/security-controls-v2-backup-recovery) voor meer informatie.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: controleren op regel matige automatische back-ups

**Richt lijnen**: Zorg ervoor dat u een back-up van systemen en gegevens maakt om bedrijfs continuïteit na een onverwachte gebeurtenis te hand haven. Dit moet richt lijnen zijn voor de doel stellingen voor herstel punt doelstelling (RPO) en de beoogde herstel tijd (RTO).

Schakel Azure Backup in en configureer de back-upbron (bijvoorbeeld Azure Vm's, SQL Server, HANA-data bases of bestands shares), evenals de gewenste frequentie en retentie periode.

Voor een hoger redundantie niveau kunt u geografisch redundante opslag optie inschakelen om back-upgegevens naar een secundaire regio te repliceren en te herstellen met behulp van een herstel bewerking voor meerdere regio's.

- [Bedrijfscontinuïteit en herstel na noodgevallen](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Azure Backup inschakelen](/azure/backup/) 

- [Hoe kan ik herstellen naar een andere regio instellen?](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Een plan voor bedrijfs continuïteit en herstel na nood gevallen instellen in het virtuele bureau blad van Windows](disaster-recovery.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="br-2-encrypt-backup-data"></a>BR-2: back-upgegevens versleutelen

**Hulp**: Zorg ervoor dat uw back-ups worden beschermd tegen aanvallen. Hierbij moet de back-ups worden versleuteld om te worden beveiligd tegen verlies van vertrouwelijkheid.

Voor reguliere back-ups van Azure service worden back-upgegevens automatisch versleuteld met door Azure-platforms beheerde sleutels. U kunt ervoor kiezen om de back-up te versleutelen met door de klant beheerde sleutel. Zorg er in dit geval voor dat deze door de klant beheerde sleutel in de sleutel kluis zich ook in het back-upbereik bevindt.

Gebruik op rollen gebaseerd toegangs beheer in Azure Backup, Azure Key Vault of andere bronnen voor het beveiligen van back-ups en door de klant beheerde sleutels. Daarnaast kunt u geavanceerde beveiligings functies inschakelen om multi-factor Authentication te vereisen voordat back-ups kunnen worden gewijzigd of verwijderd.

Overzicht van beveiligings functies in Azure Backup/Azure/backup/Security-Overview 

- [Versleuteling van back-upgegevens met door de klant beheerde sleutels](/azure/backup/encryption-at-rest-with-cmk) 

- [Back-ups maken van Key Vault sleutels in azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Beveiligings functies voor het beveiligen van hybride back-ups van aanvallen](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Valideer alle back-ups, inclusief door de klant beheerde sleutels

**Hulp**: het wordt aangeraden de gegevens integriteit op back-upmedia regel matig te valideren door een herstel proces voor gegevens uit te voeren om ervoor te zorgen dat de back-up correct werkt.

- [Bestanden herstellen vanuit back-up van Azure virtual machine](/azure/backup/backup-azure-restore-files-from-vm)

- [Beveiligings implementatie](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

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

-   Gebruik van voorzieningen voor gegevensbescherming van Azure en derden

-   Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

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

- [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure Security Benchmark: Identity management](/azure/automation/update-management/overview) (Azure Security Benchmark: identiteitsbeheer)

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

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
