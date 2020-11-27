---
title: Azure-beveiligings basislijn voor Azure HPC-cache
description: De beveiligings basislijn van de Azure HPC-cache biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8a1202d4c29f7e298346696dfc11b40326758029
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301736"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure-beveiligings basislijn voor Azure HPC-cache

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Microsoft Azure HPC-cache. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op de Azure HPC-cache. **Besturings elementen** die niet van toepassing zijn op de Azure HPC-cache, zijn uitgesloten.

Als u wilt zien hoe Azure HPC cache volledig is toegewezen aan de Azure Security-Bench Mark, raadpleegt u het [volledige Azure HPC-cache geheugen voor beveiligings basislijn toewijzing](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Richt lijnen**: wanneer u Azure HPC-cache resources implementeert, moet u een bestaand virtueel netwerk maken of gebruiken. 

Zorg ervoor dat alle virtuele netwerken van Azure een bedrijfs segmentatie principe volgen die afstemt op de bedrijfs Risico's. Elk systeem dat een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtueel netwerk en voldoende is beveiligd met een netwerk beveiligings groep (NSG) en/of Azure Firewall.

Er moeten twee aan het netwerk gerelateerde vereisten worden ingesteld voordat u uw cache kunt gebruiken: 

- Een toegewezen subnet voor het Azure HPC-cache-exemplaar

- DNS-ondersteuning zodat de cache toegang kan krijgen tot opslag en andere bronnen

Voor de Azure HPC-cache is een toegewezen subnet met de volgende kwaliteiten vereist: 

- Voor het subnet moeten ten minste 64 IP-adressen beschikbaar zijn.

- Het subnet kan geen andere Vm's hosten, zelfs voor gerelateerde services zoals client machines.

- Als u meerdere Azure HPC-cache-exemplaren gebruikt, heeft elk een eigen subnet nodig.

De best practice is het maken van een nieuw subnet voor elke cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

Als u HPC-cache wilt gebruiken met on-premises NAS-opslag, moet u ervoor zorgen dat bepaalde poorten in het on-premises netwerk onbeperkt verkeer toestaan van het subnet van de Azure HPC-cache. 

- [Poorten voor NFS-opslag toegang configureren](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Een netwerk beveiligings groep met beveiligings regels maken](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Hulp**: gebruik Azure ExpressRoute of Azure Virtual Private Network (VPN) om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en Azure ExpressRoute. 

Als u twee of meer virtuele netwerken in azure met elkaar wilt verbinden, gebruikt u peering op virtueel netwerk. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../expressroute/expressroute-connectivity-models.md) 

- [Overzicht van Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering op virtueel netwerk](../virtual-network/virtual-network-peering-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: toegang tot privé-netwerk tot stand brengen met Azure-Services

**Hulp**: Azure Virtual Network Service-eind punten gebruiken om veilige toegang tot HPC-cache te bieden. Service-eind punten zijn een geoptimaliseerde route via het Azure-backbone-netwerk zonder het Internet te passeren. 

HPC-cache biedt geen ondersteuning voor het gebruik van een persoonlijke Azure-koppeling om de beheer eindpunten te beveiligen met een particulier netwerk. 

Persoonlijke toegang is een extra ingrijpende maat regel, naast verificatie en verkeers beveiliging die wordt geboden door Azure-Services.

- [Informatie over Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Meer informatie over het koppelen van de Azure HPC-cache](hpc-cache-mount.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Richt lijnen**: Beveilig uw HPC-cache-resources tegen aanvallen van externe netwerken, waaronder DDoS-aanvallen (Distributed Denial of service), toepassingsspecifieke aanvallen, en ongevraagd en mogelijk schadelijk Internet verkeer. 

Azure bevat systeem eigen mogelijkheden voor deze beveiliging: 

- Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. 
- Bescherm uw assets tegen DDoS-aanvallen door DDoS Standard-beveiliging in te scha kelen op uw virtuele Azure-netwerken. 
- Gebruik Azure Security Center om onjuiste configuratie Risico's met betrekking tot uw netwerk bronnen te detecteren.

Azure HPC cache is niet bedoeld voor het uitvoeren van webtoepassingen. u hoeft geen aanvullende instellingen te configureren of extra netwerk services te implementeren om deze te beveiligen tegen aanvallen van buitenaf die webtoepassingen doen.

- [Documentatie over Azure Firewall](/azure/firewall/) 

- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: gebruik Azure Firewall met filtering op basis van Threat Intelligence om berichten van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. 

Wanneer Payload-inspectie vereist is, kunt u een externe detectie/indringing van een derde partij voor komen dat systeem-id/IP'S-oplossing van Azure Marketplace met Payload-inspectie mogelijkheden worden geïmplementeerd. U kunt er ook voor kiezen om op host gebaseerde ID'S/IP-adressen te gebruiken, of een EDR-oplossing (op basis van een host) in combi natie met of in plaats van op netwerk gebaseerde ID'S/IP-adressen.

Opmerking: als u een wettelijke of andere vereiste hebt voor het gebruik van ID'S/IP-adressen, moet u ervoor zorgen dat deze altijd is afgestemd op waarschuwingen van hoge kwaliteit voor uw SIEM-oplossing.

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md) 

- [Mogelijkheden van derden-ID'S van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Micro soft Defender ATP EDR-mogelijkheid](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor aanbiedingen die kunnen worden geïmplementeerd in azure Virtual Networks of die de mogelijkheid hebben om groeperingen van toegestane IP-bereiken te definiëren voor efficiënt beheer. HPC-cache biedt momenteel geen ondersteuning voor service tags. 

De best practice is het maken van een nieuw subnet voor elke cache. U kunt een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: secure Domain Name Service (DNS)

**Richt lijnen**: Volg de aanbevolen procedures voor DNS-beveiliging om te voor komen dat veelvoorkomende aanvallen zoals Dangling DNS, DNS-versterkingen, DNS-vergiftiging en spoofing en anderen.

Azure HPC-cache heeft DNS nodig om toegang te krijgen tot bronnen buiten het persoonlijke virtuele netwerk van de cache. Als uw werk stroom bronnen buiten Azure bevat, moet u ook uw eigen DNS-server instellen en beveiligen, naast het gebruik van Azure DNS.

- Gebruik Azure DNS om toegang te krijgen tot Azure Blob Storage-eind punten, op Azure gebaseerde client machines of andere Azure-resources.
- Als u toegang wilt krijgen tot on-premises opslag of als u verbinding wilt maken met de cache vanaf clients buiten Azure, moet u een aangepaste DNS-server maken die deze hostnamen kan omzetten.
- Als uw werk stroom zowel interne als externe resources bevat, stelt u uw aangepaste DNS-server in voor het door sturen van Azure-specifieke oplossings aanvragen naar de Azure DNS-server. 

Wanneer Azure DNS als gezaghebbende DNS-service wordt gebruikt, moet u ervoor zorgen dat DNS-zones en-records worden beveiligd tegen onbedoelde of schadelijke wijzigingen met behulp van Azure RBAC en resource vergrendelingen.

Als u uw eigen DNS-server wilt configureren, moet u de volgende beveiligings richtlijnen volgen:

- [Implementatie handleiding voor beveiligde Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen](../security/fundamentals/subdomain-takeover.md) 

- [Meer informatie over DNS-toegangs vereisten voor HPC-cache](hpc-cache-prerequisites.md#dns-access)

- [Overzicht van Azure DNS](../dns/dns-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Ga voor meer informatie naar [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits- en verificatiesysteem

**Richt lijnen**: Azure HPC-cache is niet geïntegreerd met Azure Active Directory voor interne bewerkingen. Azure AD kan echter worden gebruikt voor het verifiëren van gebruikers in de Azure Portal of CLI om HPC-cache-implementaties en gerelateerde onderdelen te maken, weer te geven en te beheren.

Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer in Azure. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben bij het nemen van beveiligingsmaatregelen voor de cloud in uw organisatie. Azure AD biedt een beveiligde score voor identiteiten om u te helpen bij het beoordelen van identiteits beveiliging postuur ten opzichte van de aanbevelingen van micro soft best practice. Gebruik de score om te meten hoe goed uw configuratie aansluit bij de aanbevelingen en om verbeteringen door te voeren in uw beveiligingspostuur.

Opmerking: Azure AD biedt ondersteuning voor externe identiteit waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Externe id-providers gebruiken voor een toepassing](/azure/active-directory/b2b/identity-providers) 

- [Wat is de identiteit van beveiligde scores in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: toepassings identiteiten veilig en automatisch beheren

**Richt lijnen**: HPC-cache maakt gebruik van door Azure beheerde identiteiten voor niet-menselijke accounts, zoals services of automatisering. Het is raadzaam om de functie Managed Identity van Azure te gebruiken in plaats van een krachtig menselijk account te maken om uw resources te openen of uit te voeren. 

HPC-cache kan systeem eigen verificatie uitvoeren bij Azure-Services/-bronnen die ondersteuning bieden voor Azure AD-verificatie via vooraf gedefinieerde regels voor toegangs toekenning. Zo voor komt u dat er hardcoded referenties in de bron code-of configuratie bestanden moeten worden gebruikt.

- [Door Azure beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) 

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding van Azure AD gebruiken voor toegang tot toepassingen

**Richt lijnen**: Azure HPC cache wordt niet geïntegreerd met Azure AD voor interne bewerkingen. Azure AD kan echter worden gebruikt voor het verifiëren van gebruikers in de Azure Portal of CLI om HPC-cache-implementaties en gerelateerde onderdelen te maken, weer te geven en te beheren.

Azure Active Directory biedt identiteits-en toegangs beheer aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit geldt niet alleen voor identiteiten zoals werknemers, maar ook voor externe identiteiten zoals partners en leveranciers. Hierdoor is eenmalige aanmelding mogelijk voor het beheren en beveiligen van toegang tot gegevens en resources van uw organisatie, zowel on-premises als in de cloud. Koppel al uw gebruikers, toepassingen en apparaten aan Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Krachtige verificatiemechanismen gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Hoewel Azure HPC cache niet kan worden geïntegreerd met Azure AD voor interne bewerkingen, kan Azure AD worden gebruikt voor het verifiëren van gebruikers in de Azure portal of CLI om HPC-cache-implementaties en gerelateerde onderdelen te maken, weer te geven en te beheren.  

Azure AD biedt ondersteuning voor sterke verificatie controles via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.

- Multi-factor Authentication: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor een aantal aanbevolen procedures in de MFA-installatie. MFA kan worden afgedwongen voor alle gebruikers, gebruikers selecteren of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.
- Verificatie zonder wachtwoord: Er zijn drie opties beschikbaar voor verificatie zonder wachtwoord: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Zorg ervoor dat u voor beheerders en bevoegde gebruikers het hoogste niveau van de sterke verificatie methode gebruikt. Implementeer het juiste beleid voor sterke authenticatie voor andere gebruikers.

Azure HPC cache biedt ook ondersteuning voor verouderde verificatie op basis van wacht woorden voor client systemen die verbinding maken met de cache. Dit soort verbindingen omvat alleen Cloud accounts (gebruikers accounts die rechtstreeks in azure zijn gemaakt) die een basislijn wachtwoord beleid of Hybrid accounts (gebruikers accounts die afkomstig zijn van on-premises Active Directory) die de on-premises wachtwoord beleid volgen. 

Wanneer u gebruikmaakt van verificatie op basis van wacht woorden, biedt Azure AD een functie voor wachtwoord beveiliging waarmee wordt voor komen dat gebruikers wacht woorden kunnen instellen die gemakkelijk te raden zijn. Micro soft biedt een globale lijst met verboden wacht woorden die worden bijgewerkt op basis van de telemetrie en klanten kunnen de lijst uitbreiden op basis van hun behoeften (bijvoorbeeld met betrekking tot huis stijl, culturele verwijzingen enzovoort). Deze wachtwoord beveiliging kan worden gebruikt voor Cloud-en hybride accounts.

Opmerking: verificatie op basis van wachtwoord referenties is alleen vatbaar voor populaire aanvals methoden. Voor een betere beveiliging gebruikt u sterke verificatie zoals MFA en een sterk wachtwoord beleid. Als u toepassingen van derden en Marketplace-Services gebruikt die standaard wachtwoorden hebben, stelt u een nieuw, beveiligd wacht woord in wanneer u de service voor het eerst instelt. 

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Controleren op accountafwijkingen en hiervoor waarschuwen

**Richt lijnen**: Azure HPC Cache kan Azure Active Directory gebruiken voor gebruikers beheer vanuit de Azure Portal.  Azure Active Directory biedt de volgende gegevens bronnen:

- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen en de afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Activiteiten rapporten controleren in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

 

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../security-center/alerts-reference.md) 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Chat-7: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: niet van toepassing; Met HPC cache kunnen klanten geen blijvende gegevens in de actieve omgeving implementeren.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Ga voor meer informatie naar [Azure Security-benchmark: Bevoegde toegang](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Hulp**: HPC-cache maakt gebruik van Azure RBAC om de toegang tot bedrijfskritische systemen te isoleren door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Voor het maken van een cache vereist HPC-cache dat gebruikers voldoende bevoegdheden hebben in het abonnement om Nic's te maken. Bij gebruik van Blob Storage zijn de Inzender voor Azure rollen storage-opslag account en Inzender voor opslag-blobs vereist voor de HPC-cache voor toegang tot de opslag. 

Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw bedrijfskritische assets, zoals Active Directory-domein controllers (Dc's), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te manipuleren.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [RBAC-machtigingen voor de Azure HPC-cache](hpc-cache-prerequisites.md#permissions)

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig beoordelen en afstemmen

**Richt lijnen**: Controleer de gebruikers accounts en toegangs toewijzingen regel matig om ervoor te zorgen dat de accounts en hun toegangs niveaus geldig zijn. 

Azure HPC cache kan Azure Active Directory (Azure AD)-accounts gebruiken voor het beheren van gebruikers toegang via de Azure Portal en gerelateerde interfaces. Azure AD biedt toegangs beoordelingen waarmee u groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen kunt controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management worden geconfigureerd om te waarschuwen wanneer er een uitzonderlijk groot aantal beheerders accounts worden gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Opmerking: sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

Wanneer u NFS-opslag doelen gebruikt, moet u samen werken met uw netwerk beheerders en firewall beheerders om de toegangs instellingen te controleren en ervoor te zorgen dat Azure HPC cache kan communiceren met de NFS-opslag systemen.

- [Voor een lijst met HPC-cache machtigingen raadpleegt u de vereisten voor de Azure HPC-cache](hpc-cache-prerequisites.md) 

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: HPC-Cache kan Azure Active Directory gebruiken voor het beheren van toegang tot bronnen via de Azure Portal. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: HPC-Cache kan Azure Active Directory gebruiken om de toegang tot de cache bronnen via de Azure portal te beheren. 

Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren. 

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md) 

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Richt lijnen**: HPC-cache is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. 

De bevoegdheden die u toewijst aan resources via Azure RBAC, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de just-in-time (JIT) benadering van Azure AD Privileged Identity Management (PIM) en moet regel matig worden gecontroleerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md) 

- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Ga voor meer informatie naar [Azure Security-benchmark: Gegevensbeveiliging](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: gevoelige gegevens detecteren, classificeren en labelen 

**Hulp**: HPC-cache beheert gevoelige gegevens, maar beschikt niet over de mogelijkheid om gevoelige gegevens te detecteren, classificeren en labelen.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beveiligen

**Richt lijnen**: Beveilig gevoelige gegevens door de toegang te beperken met behulp van Azure-rol op basis van Access Control (Azure RBAC), op het netwerk gebaseerde toegangs beheer functies en specifieke besturings elementen in Azure-Services (zoals VERSLEUTELING in SQL en andere data bases).

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren op niet-geautoriseerde overdracht van gevoelige gegevens

**Hulp**: HPC-cache verzendt gevoelige gegevens, maar biedt geen ondersteuning voor bewaking voor niet-geautoriseerde overdracht van gevoelige gegevens.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: HPC-cache ondersteunt gegevens versleuteling tijdens de overdracht met TLS v 1.2 of hoger.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Gevoelige data-at-rest versleutelen

**Richt lijnen**: als u een aanvulling op toegangs controles wilt uitvoeren, moeten de gegevens in rust worden beschermd tegen buiten-band-aanvallen (bijvoorbeeld toegang tot onderliggende opslag) met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure biedt standaard versleuteling voor gegevens op rest. Voor zeer gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling in rust op alle Azure-resources waar beschikbaar. Azure beheert standaard uw versleutelings sleutels, maar Azure biedt opties voor het beheren van uw eigen sleutels (door de klant beheerde sleutels) voor bepaalde Azure-Services.

Alle gegevens die zijn opgeslagen in azure, met inbegrip van de cache schijven, worden standaard versleuteld met behulp van door micro soft beheerde sleutels. U hoeft alleen de instellingen voor de Azure HPC-cache aan te passen als u de sleutels wilt beheren die worden gebruikt voor het versleutelen van uw gegevens.

Als dat nodig is voor de naleving van de reken bronnen, implementeert u een hulp programma van derden, zoals een geautomatiseerde oplossing voor gegevens verlies op basis van een host voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

- [Door de klant beheerde versleutelings sleutels gebruiken met Azure HPC cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Door de klant beheerde versleutelings sleutels configureren](/azure/storage/common/storage-encryption-keys-portal) 

- [Versleutelings model en sleutel beheer tabel](../security/fundamentals/encryption-atrest.md)

 

- [Gegevens bij dubbele versleuteling in de rest van Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Ga voor meer informatie naar [Azure Security-benchmark: Asset-management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Azure HPC cache ondersteunt het gebruik van tags. Pas Tags toe op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. 

U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie. Labels kunnen worden toegevoegd bij het maken van een cache en nadat de cache is geïmplementeerd. 

Gebruik Azure virtual machine Inventory om het verzamelen van informatie over software op Virtual Machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u toegang wilt krijgen tot installatie datum en andere informatie, schakelt u Diagnostische gegevens op gast niveau in en brengt u de Windows-gebeurtenis Logboeken in een Log Analytics-werk ruimte.
HPC-cache staat het uitvoeren van een toepassing of installatie van software op de bijbehorende resources niet toe. 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: HPC-Cache ondersteunt Azure Resource Manager-implementaties. Gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richt lijnen**: niet van toepassing. De HPC-cache van Azure kan niet worden gebruikt om de beveiliging van assets in een levenscyclus beheer proces te garanderen. Het is de verantwoordelijkheid van de klant om kenmerken en netwerk configuraties van activa te onderhouden die als hoge impact worden beschouwd. 

Het is raadzaam dat de klant een proces maakt om het kenmerk en de wijzigingen in de netwerk configuratie vast te leggen, de wijzigings impact te meten en zo nodig herstel taken te maken.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en bedreigingsdetectie

*Ga voor meer informatie naar [Azure Security-benchmark: Logboekregistratie en bedreigingsdetectie](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Hulp**: gebruik de Azure Security Center ingebouwde functie voor detectie van bedreigingen en schakel Azure Defender (formeel Azure Advanced Threat Protection) in voor uw HPC-cache-resources. Azure Defender voor HPC cache biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of misbruik van uw cache bronnen worden gedetecteerd.

Door sturen van logboeken van HPC-cache naar uw SIEM die kunnen worden gebruikt voor het instellen van aangepaste bedreigings detecties. Zorg ervoor dat u verschillende typen Azure-assets bewaakt voor mogelijke dreigingen en afwijkingen. Richt u op het ophalen van waarschuwingen met hoge kwaliteit om te voor komen dat de fout-positieven worden gereduceerd. Waarschuwingen kunnen worden gebrond op basis van logboek gegevens, agents of andere gegevens.

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection) 

- [Naslag Gids voor beveiligings waarschuwingen Azure Security Center](../security-center/alerts-reference.md) 

- [Aangepaste analyse regels maken voor het detecteren van bedreigingen](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyber Threat Intelligence met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Richt lijnen**: Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel of andere hulpprogram MA'S voor Siem/controle voor geavanceerde bewakings-en analyse-gebruiks voorbeelden:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Audit logboeken: bieden traceer baarheid via Logboeken voor alle wijzigingen die worden uitgevoerd door diverse functies in azure AD. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.

- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Met deze mogelijkheid kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Activiteiten rapporten controleren in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Richt lijnen**: u kunt gebruikmaken van VPN-gateways en hun functionaliteit voor het vastleggen van pakketten, naast de algemeen beschik bare hulpprogram ma's voor het vastleggen van pakketten om netwerk pakketten vast te leggen tussen uw virtuele netwerken.

Implementeer een netwerk beveiligings groep in het netwerk waarin uw Azure HPC-cache resources worden geïmplementeerd. Schakel logboeken stroom voor netwerk beveiligings groep in de netwerk beveiligings groepen in voor het controleren van verkeer.

Uw stroom logboeken worden bewaard in een opslag account. Schakel de Traffic Analytics oplossing in om die stroom logboeken te verwerken en te verzenden naar een Log Analytics-werk ruimte. Traffic Analytics biedt extra inzicht in de verkeers stroom voor uw Azure-netwerken. Traffic Analytics kan u helpen bij het visualiseren van netwerk activiteiten en het identificeren van HOTS pots, het identificeren van beveiligings dreigingen, het begrijpen van verkeers patronen en het lokaliseren van onjuiste netwerk configuraties.

De cache heeft DNS nodig om toegang te krijgen tot bronnen buiten het virtuele netwerk. Afhankelijk van de bronnen die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en door sturen tussen die server en Azure DNS servers configureren. 

Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie van oplossingen conform uw organisatie.

- [Pakket opnames voor VPN-gateways configureren](../vpn-gateway/packet-capture.md) 

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md) 

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md) 

- [Meer informatie over de vereisten voor DNS](hpc-cache-prerequisites.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure HPC-cache resources maken automatisch activiteiten Logboeken. Deze logboeken bevatten alle schrijf bewerkingen (PUT, POST, DELETE), maar bevatten geen lees bewerkingen (GET). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

U kunt ook Azure Security Center en Azure Policy gebruiken om Azure-resource Logboeken in te scha kelen voor HPC-cache en om gegevens te verzamelen en te registreren. Deze logboeken kunnen essentieel zijn voor het later onderzoeken van beveiligings incidenten en het uitvoeren van forensische-oefeningen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens, en vereisten voor het bewaren van gegevens.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Ga voor meer informatie naar [Azure Security-benchmark: Reageren op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding – proces voor reageren op incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat er processen aanwezig zijn in uw organisatie om te reageren op beveiligingsincidenten, dat deze processen zijn bijgewerkt voor Azure en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging implementeren binnen de onderneming](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor reageren op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: voor bereiding-incident melding instellen 

**Richtlijnen**: Definieer in Azure Security Center contactgegevens voor beveiligingsincidenten. Deze contactgegevens worden gebruikt door Microsoft om contact met u op te nemen als Microsoft Security Response Center (MSRC) heeft vastgesteld dat uw gegevens zijn geraadpleegd door een onrechtmatige of niet-geautoriseerde partij. U hebt ook de mogelijkheid om waarschuwingen en meldingen voor incidenten aan te passen in verschillende Azure-services, op basis van de behoeften van de reactie op incidenten. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse - incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u leren van eerdere incidenten en prioriteit toekennen aan waarschuwingen voor analisten, zodat ze geen tijd verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: incidenten prioriteren

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke opzet is achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, eliminatie en herstel - verwerking van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige terugkerende taken om de responstijd te versnellen en de belasting van analisten te verlagen. Handmatige taken nemen meer tijd in beslag, waardoor elk incident wordt vertraagd en een analist minder incidenten kan verwerken. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="posture-and-vulnerability-management"></a>Beheer van beveiligingspostuur en beveiligingsproblemen

*Ga voor meer informatie naar [Azure Security-benchmark: Beheer van beveiligingspostuur en beveiligingsproblemen](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: veilige configuraties voor reken bronnen instellen

**Hulp**: gebruik Azure Security Center en Azure Policy om veilige configuraties te maken voor alle reken resources, waaronder vm's, containers en anderen.

- [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md) 

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmatig aanvalssimulaties uitvoeren

**Richtlijnen**: Voer zo nodig penetratietests of Red Teaming-activiteiten uit voor uw Azure-resources en zorg ervoor dat alle beveiligingsproblemen worden opgelost.
Volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests voldoen aan de beleidsregels van Microsoft. Volg de strategie en instructies van Microsoft voor Red Teaming en penetratietests van live sites voor cloudinfrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietests in Azure](../security/fundamentals/pen-testing.md)

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Bench Mark: Backup and Recovery](/azure/security/benchmarks/security-controls-v2-backup-recovery)(Engelstalig) voor meer informatie.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: controleren op regel matige automatische back-ups

**Richt lijnen**: omdat Azure HPC cache een cache oplossing is en niet een opslag systeem, kunt u zich richten op het regel matig maken van een back-up van de gegevens in de opslag doelen. Volg de standaard procedures voor Azure Blob-containers en voor het maken van back-ups van on-premises opslag doelen. 

Om onderbrekingen in het geval van een regionale onderbreking tot een minimum te beperken, kunt u stappen ondernemen om toegang te krijgen tot gegevens in meerdere regio's.  

Elk Azure HPC-cache-exemplaar wordt uitgevoerd binnen een bepaald abonnement en in één regio. Dit betekent dat uw cache werk stroom mogelijk kan worden onderbroken als de regio een volledige onderbreking heeft. Om deze onderbreking te minimaliseren, moet de organisatie back-end-opslag gebruiken die toegankelijk is vanuit meerdere regio's. Deze opslag kan een on-premises NAS-systeem zijn met de juiste DNS-ondersteuning of Azure Blob-opslag die zich in een andere regio bevindt dan de cache.

Als uw werk stroom in uw primaire regio wordt voortgezet, worden gegevens opgeslagen in de lange termijn opslag buiten de regio. Als de cache regio niet beschikbaar is, kunt u een duplicaat van een Azure HPC-cache-exemplaar maken in een secundaire regio, verbinding maken met dezelfde opslag en het werk vanuit de nieuwe cache hervatten.

- [Meer informatie over regionale failover](hpc-region-recovery.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="br-2-encrypt-backup-data"></a>BR-2: back-upgegevens versleutelen

**Hulp**: Zorg ervoor dat uw back-ups worden beschermd tegen aanvallen. Hierbij moet de back-ups worden versleuteld om te worden beveiligd tegen verlies van vertrouwelijkheid.

Voor on-premises back-ups met Azure Backup, wordt versleuteling op rest aangeboden met de wachtwoordzin die u opgeeft. Voor reguliere back-ups van Azure service worden back-upgegevens automatisch versleuteld met door Azure-platforms beheerde sleutels. U kunt ervoor kiezen om de back-up te versleutelen met door de klant beheerde sleutels. In dit geval moet u ervoor zorgen dat deze door de klant beheerde sleutel in de sleutel kluis zich ook in het back-upbereik bevindt.

De HPC-cache van Azure wordt ook beveiligd door de VM-host-versleuteling op de beheerde schijven die uw gegevens in de cache bevatten, zelfs als u een klant sleutel toevoegt voor de cache schijven. Het toevoegen van een door de klant beheerde sleutel voor dubbele versleuteling biedt een extra beveiligings niveau voor klanten met hoge beveiligings behoeften. Lees de versleuteling aan de server zijde van Azure Disk Storage voor meer informatie.

Gebruik op rollen gebaseerd toegangs beheer in Azure Backup, Azure Key Vault of andere bronnen voor het beveiligen van back-ups en door de klant beheerde sleutels. Daarnaast kunt u geavanceerde beveiligings functies inschakelen om MFA te vereisen voordat back-ups kunnen worden gewijzigd of verwijderd.

- [VM-host versleuteling](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Versleuteling aan de server zijde van Azure Disk Storage](../virtual-machines/disk-encryption.md)

- [Overzicht van beveiligings functies in Azure Backup](../backup/security-overview.md) 

- [Versleuteling van back-upgegevens met door de klant beheerde sleutels](../backup/encryption-at-rest-with-cmk.md)  

- [Een back-up maken van Key Vault sleutels in azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: zorg er regel matig voor dat u een back-up van door de klant beheerde sleutels kunt herstellen.

- [Key Vault sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: risico op verloren sleutels beperken

**Richt lijnen**: Zorg ervoor dat u maat regelen hebt om te voor komen en te herstellen van het verlies van sleutels. Schakel de beveiliging van zacht verwijderen en opschonen in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Zacht verwijderen en beveiliging opschonen inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Ga voor meer informatie naar [Azure Security-benchmark: Governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor Asset Management en gegevensbescherming definiëren 

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Stel prioriteiten in voor detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

-   Zichtbaarheid van de beveiligingsorganisaties ten aanzien van risico's en asset-inventaris 

-   Goedkeuring van beveiligingsorganisatie van te gebruiken Azure-services 

-   Beveiliging van assets gedurende de gehele levenscyclus

-   Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

-   Gebruik van mogelijkheden van Azure native en gegevens bescherming van derden

-   Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

-   Juiste cryptografische normen

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie voor bedrijf definiëren 

**Richtlijnen**: Stel een bedrijfsbrede strategie in om de toegang tot assets te segmenteren met behulp van een combinatie van maatregelen voor identiteit, netwerken, toepassingen, abonnementen, beheergroepen en andere maatregelen.

Zoek een compromis tussen de noodzaak om beveiligingsaspecten te scheiden en de dagelijkse werking van de systemen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle soorten maatregelen, zoals netwerkbeveiliging, identiteits- en toegangsmodellen, en modellen voor toepassingsmachtigingen/toegangs en maatregelen voor menselijke processen.

- [Guidance on segmentation strategy in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Guidance on segmentation strategy in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Align network segmentation with enterprise segmentation strategy](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor beheer van beveiligingspostuur definiëren

**Richtlijnen**: U moet de risico's voor afzonderlijke assets en de omgeving waarin ze worden gehost, voortdurend meten en beperken. Geef prioriteit aan waardevolle assets en gebieden die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten in het netwerk voor binnenkomend en uitgaand verkeer, eindpunten voor gebruikers en beheerders, enzovoort.

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Rollen en verantwoordelijkheden binnen de organisatie in overeenstemming brengen

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligingsorganisatie documenteert en communiceert. Bepaal de prioriteit door een duidelijke verantwoordelijkheid te definiëren voor beveiligingsbeslissingen, het bekend maken van het personeel met het gedeelde verantwoordelijkheidsmodel en het trainen van technische teams in technologie om de cloud te beveiligen.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Beveiligingsstrategie voor netwerk definiëren

**Richtlijnen**: Stel een aanpak voor Azure-netwerkbeveiliging op als onderdeel van de algehele strategie voor het toegangsbeheer van uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerkbeheer en beveiligingsverantwoordelijkheid

-   Model voor segmentatie van het virtuele netwerk in lijn met de segmentatiestrategie van het bedrijf

-   Herstelstrategie voor verschillende bedreigings- en aanvalsscenario's

-   Strategie voor internetperiferie en inkomend en uitgaand

-   Strategie voor interconnectiviteit tussen hybride cloud en on-premises

-   Up-to-date artefacten voor netwerkbeveiliging (zoals netwerkdiagrammen en referentienetwerkarchitectuur)

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:
- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure network security overview](../security/fundamentals/network-overview.md)

- [Enterprise network architecture strategy](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor identiteit en bevoegde toegang definiëren

**Richtlijnen**: Stel een aanpak voor Azure-identiteit en bevoegde toegang op als onderdeel van de algehele strategie voor het toegangsbeheer van uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits- en verificatiesysteem en de interconnectiviteit ervan met andere interne en externe identiteitssystemen

-   Krachtige authenticatiemethoden in verschillende use-cases en onder diverse omstandigheden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Controle en afhandeling van afwijkende gebruikersactiviteiten  

-   Beoordelings- en afstemmingsproces voor gebruikers-id's en -toegang

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure identity management security overview](../security/fundamentals/identity-management-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons bij bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Volg prioriteiten voor het leveren van analisten met hoogwaardige waarschuwingen en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van het SecOps-team van de organisatie 

-   Een goed omschreven responsproces voor incidenten dat is afgestemd op NIST of een ander framework uit de branche 

-   Logboeken registreren en bewaren voor ondersteuning van bedreigingsdetectie, respons op incidenten en nalevingsbehoeften

-   Gecentraliseerde zichtbaarheid van en correlatiegegevens over bedreigingen, met behulp van SIEM, functies van Azure en andere bronnen 

-   Plan voor communicatie en melding aan klanten, leveranciers en openbare betrokken partijen

-   Gebruik van platforms van Azure en van derden voor het afhandelen van incidenten, zoals logboekregistratie en bedreigingsdetectie, forensics, en herstel en eliminatie van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijsbehoud

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Lees [dit overzichtsartikel over Azure Security Benchmark V2](/azure/security/benchmarks/overview).
- Lees meer over [basislijnen voor de beveiliging van Azure](/azure/security/benchmarks/security-baselines-overview)
