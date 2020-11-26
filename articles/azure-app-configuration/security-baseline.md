---
title: Azure-beveiligings basislijn voor Azure-app configuratie
description: De beveiligings basislijn van Azure-app configuratie bevat procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 61dc3b9376737f89643473dffc3c915d3e0d9c44
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183444"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure-beveiligings basislijn voor Azure-app configuratie

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Azure-app configuratie. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure-app configuratie. **Besturings elementen** die niet van toepassing zijn op Azure-app configuratie, zijn uitgesloten.

Zie voor meer informatie over hoe Azure-app configuratie volledig is toegewezen aan de beveiligings benchmark van Azure, het [volledige Azure-app configuratie beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-controls-v2-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp**: de Azure-app-configuratie implementeert geen resources rechtstreeks in een virtueel netwerk. Omdat de service niet is geïmplementeerd in een virtueel netwerk, kunt u bepaalde netwerk functies niet gebruiken om het interne verkeer van de service te beveiligen, zoals: netwerk beveiligings groepen, route tabellen of andere netwerk apparaten, zoals een Azure Firewall. Met app-configuratie kunt u echter privé-eind punten gebruiken om veilig verbinding te maken met Azure-app configuratie van een virtueel netwerk.

Gebruik Azure Sentinel om het gebruik van verouderde onveilige protocollen, zoals SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niet-ondertekende LDAP-bindingen en zwakke versleuteling in Kerberos, te detecteren.

- [Privé-eind punten gebruiken voor Azure-app configuratie](concept-private-endpoint.md)

- [Azure Sentinel Inveilige protocollen werkmap](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Hulp**: de configuratie van Azure-app biedt ondersteuning voor het gebruik van privé-eind punten voor het veilig verzenden van gegevens via een privé-koppeling. Gebruik Azure ExpressRoute of het virtuele particuliere netwerk (VPN) van Azure om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en Azure ExpressRoute.

Als u twee of meer virtuele netwerken in azure met elkaar wilt verbinden, gebruikt u peering op virtueel netwerk. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../expressroute/expressroute-connectivity-models.md)

- [Overzicht van Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering op virtueel netwerk](../virtual-network/virtual-network-peering-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: toegang tot privé-netwerk tot stand brengen met Azure-Services

**Hulp**: persoonlijke Azure-koppeling gebruiken om persoonlijke toegang tot Azure-app configuratie vanuit uw virtuele netwerken mogelijk te maken zonder het Internet te passeren.

Persoonlijke toegang is een extra verdedigings maatregel naast verificatie en verkeers beveiliging die wordt aangeboden door Azure-Services.

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Een persoonlijke koppeling instellen in Azure-app configuratie](concept-private-endpoint.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Richt lijnen**: wanneer u configuratie waarden via een virtueel netwerk opent, moet u uw resources beschermen tegen aanvallen van externe netwerken, waaronder DDoS-aanvallen (Distributed Denial of service), toepassingsspecifieke aanvallen, en ongevraagde en mogelijk schadelijk Internet verkeer. Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. Bescherm uw assets tegen DDoS-aanvallen door DDoS Standard-beveiliging in te scha kelen op uw virtuele Azure-netwerken. Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die betrekking hebben op uw netwerkgerelateerde bronnen.

Azure-app configuratie niet is bedoeld voor het uitvoeren van webtoepassingen, wordt de configuratie voor deze webtoepassingen geboden. U hoeft geen aanvullende instellingen te configureren of extra netwerk services te implementeren om deze te beveiligen tegen aanvallen van buitenaf die gericht zijn op webtoepassingen.

- [Documentatie over Azure Firewall](../firewall/index.yml)

- [Azure DDoS Protection Standard beheren met de Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: gebruik Azure Firewall met filtering op basis van bedreigings informatie om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. Wanneer Payload inspectie vereist is, kunt u een oplossing van een derde partij/IP-adressen implementeren vanuit Azure Marketplace met Payload-inspectie mogelijkheden. U kunt er ook voor kiezen om op host gebaseerde ID'S/IP-adressen te gebruiken, of een EDR-oplossing (op basis van een host) in combi natie met of in plaats van op netwerk gebaseerde ID'S/IP-adressen.

Opmerking: als u een wettelijke of andere vereiste hebt voor het gebruik van ID'S/IP-adressen, moet u ervoor zorgen dat deze altijd is afgestemd op waarschuwingen van hoge kwaliteit voor uw SIEM-oplossing.

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace bevat mogelijkheden voor externe ID'S van derden](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Micro soft Defender ATP EDR-mogelijkheid](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: Azure Virtual Network Service-Tags gebruiken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall geconfigureerd voor uw app-configuratie resources. U kunt de service label ' AppConfiguration ' gebruiken in plaats van specifieke IP-adressen bij het maken van beveiligings regels voor uitgaand verkeer in het netwerk van uw toepassing. Door de servicetag naam op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Ga voor meer informatie naar [Azure Security Benchmark: Identiteitsbeheer](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits- en verificatiesysteem

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory (Azure AD) die de standaard service voor identiteits-en toegangs beheer van Azure is. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:
- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.
- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben bij het nemen van beveiligingsmaatregelen voor de cloud in uw organisatie. Azure AD biedt een beveiligde score voor identiteiten om u te helpen bij het beoordelen van identiteits beveiliging postuur ten opzichte van de aanbevelingen van micro soft best practice. Gebruik de score om te meten hoe goed uw configuratie aansluit bij de aanbevelingen en om verbeteringen door te voeren in uw beveiligingspostuur.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Inzender: gebruik deze rol om de configuratie bron van de app te beheren. Terwijl de app-configuratie gegevens kunnen worden geopend met behulp van toegangs sleutels, verleent deze rol geen directe toegang tot de gegevens met behulp van Azure AD.

- Lezer: gebruik deze rol om Lees toegang te verlenen aan de configuratie bron van de app. Hiermee wordt geen toegang verleend tot de toegangs sleutels van de resource en evenmin op de gegevens die zijn opgeslagen in de app-configuratie.

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Wat is de identiteit van beveiligde scores in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: toepassings identiteiten veilig en automatisch beheren

**Hulp**: door Azure beheerde identiteiten gebruiken om toegang te krijgen tot Azure-app configuratie van niet-menselijke accounts, zoals andere Azure-Services. Het wordt aanbevolen Azure Managed Identity te gebruiken in plaats van een krachtig menselijk account te maken om uw resources te openen of uit te voeren om de nood zaak voor het beheren van aanvullende referenties te beperken. Azure-app configuratie kan ook een beheerde identiteit worden toegewezen om systeem eigen verificatie te kunnen uitvoeren voor andere Azure-Services/-bronnen die ondersteuning bieden voor Azure AD-verificatie. Dit kan handig zijn om eenvoudige toegang tot Azure Key Vault van de app-configuratie in te scha kelen bij het ophalen van geheimen. Wanneer beheerde identiteiten worden gebruikt, wordt de identiteit beheerd door het Azure-platform en hoeft u geen geheimen in te richten of te draaien.

Azure-app-configuratie ondersteunt dat uw toepassing twee typen identiteiten krijgt:
- Een door het systeem toegewezen identiteit is gekoppeld aan uw configuratie bron. Deze wordt verwijderd als uw configuratie bron wordt verwijderd. Een configuratie resource kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de gebruiker toegewezen identiteit is een zelfstandige Azure-resource die kan worden toegewezen aan uw configuratie bron. Een configuratie resource kan meerdere door de gebruiker toegewezen identiteiten hebben.

Wanneer beheerde identiteiten niet kunnen worden gebruikt, maakt u een service-principal met beperkte machtigingen op het niveau van de Azure-app configuratie resource. Deze service-principals met certificaat referenties configureren en terugvallen op client geheimen. In beide gevallen kunnen Azure Key Vault worden gebruikt in combi natie met door Azure beheerde identiteiten, zodat de runtime-omgeving (bijvoorbeeld een Azure function) de referentie kan ophalen uit de sleutel kluis.

- [Beheerde identiteiten gebruiken voor Azure-app configuratie](overview-managed-identity.md)

- [Door Azure beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md)

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Beheerde identiteiten gebruiken om App Configuration te openen](howto-integrate-azure-managed-service-identity.md)

- [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps) 

- [Een service-principal maken met certificaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Azure Key Vault gebruiken voor registratie van beveiligings-principal](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: in azure-app configuratie wordt gebruikgemaakt van Azure Active Directory (Azure AD) om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit geldt niet alleen voor identiteiten zoals werknemers, maar ook voor externe identiteiten zoals partners en leveranciers. Met Azure AD kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren van de app-configuratie service via de Azure Portal met behulp van een gesynchroniseerde bedrijfs Active Directory-identiteiten. Koppel al uw gebruikers, toepassingen en apparaten aan Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Krachtige verificatiemechanismen gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: in azure-app configuratie wordt gebruikgemaakt van Azure Active Directory die ondersteuning biedt voor krachtige verificatie-instellingen via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.
- Meervoudige verificatie: Schakel meervoudige verificatie van Azure AD in en volg de aanbevelingen voor identiteits- en toegangsbeheer van Azure Security Center voor enkele best practices bij de configuratie van meervoudige verificatie. Meervoudige verificatie kan worden afgedwongen voor alle of bepaalde gebruikers, of op gebruikersniveau op basis van aanmeldingsvoorwaarden en risicofactoren.
- Verificatie zonder wachtwoord: Er zijn drie opties beschikbaar voor verificatie zonder wachtwoord: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

Opmerking: MFA kan worden afgedwongen voor de gebruikers accounts die toegang hebben tot de app-configuratie en deze beheren, maar niet op programmatische service accounts. Gebruik verificatie zonder wacht woord, zoals beheerde identiteiten waar mogelijk, en dwing MFA af voor elk gebruikers account.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Controleren op accountafwijkingen en hiervoor waarschuwen

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden geleverd:

-   Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

-   Audit logboeken: voorziet in traceer baarheid via Logboeken voor alle wijzigingen die zijn aangebracht via verschillende functies in azure AD. Voor beelden van audit Logboeken in vastgelegde wijzigingen zijn het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleids regels.

-   Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

-   Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. 

Azure Advanced Threat Protection (ATP) is een beveiligings oplossing die on-premises Active Directory signalen kan gebruiken om geavanceerde bedreigingen, gemanipuleerde identiteiten en schadelijke Insider-acties te identificeren, te detecteren en te onderzoeken.

- [Activiteiten rapporten controleren in azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../security-center/alerts-reference.md)

- [Azure-activiteiten logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Verbinding maken met gegevens van Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Richt lijnen**: Azure-app configuratie ondersteunt de voorwaardelijke toegang van Azure Active Directory (Azure AD) voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken, moet MFA gebruiken voor aanmelding. Gedetailleerd beheer beleid voor verificatie sessies kan ook worden gebruikt voor verschillende use cases. Deze beleids regels voor voorwaardelijke toegang zijn alleen van toepassing op gebruikers accounts die worden geverifieerd bij Azure AD voor toegang tot en beheer van de app-configuratie service, maar ze zijn niet van toepassing op service-principals of verbindings reeksen die verbinding maken met uw configuratie bron.

- [Overzicht van voorwaardelijke toegang voor Azure](../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Verificatie sessie beheer met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Chat-7: onbedoelde referentie blootstelling elimineren

**Hulp**: met de Azure-app configuratie kunnen klanten configuraties opslaan die mogelijk identiteiten of geheimen bevatten. Het is raadzaam om referentie scanner te implementeren om referenties in configuraties te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Gebruik de Azure-app Configuration-service samen met Azure Key Vault. Sla referenties op in Key Vault en koppel deze referenties aan de hand van een Key Vault verwijzing in uw app-configuratie bron. Wanneer de app-configuratie deze verwijzingen maakt, worden de Uri's van de Key Vault waarden opgeslagen in plaats van de waarden zelf. Toepassingen kunnen verbinding maken met de app-configuratie om referenties op te halen uit Key Vault.

Voor GitHub kunt u de systeem eigen functie voor het scannen van geheime gegevens gebruiken om referenties of een andere vorm van geheimen binnen de code te identificeren.

- [Zelf studie voor het gebruik van Key Vault verwijzingen in een ASP.NET Core-app](use-key-vault-references-dotnet-core.md)

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub Secret Scanning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Ga voor meer informatie naar [Azure Security-benchmark: Bevoegde toegang](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met hoge bevoegdheden beveiligen en beperken

**Richt lijnen**: Beperk het aantal accounts of rollen met veel bevoegdheden en beveilig deze accounts op een verhoogd niveau, omdat gebruikers met deze bevoegdheid elke resource in uw Azure-omgeving direct of indirect kunnen lezen en wijzigen.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

Toegangs sleutels zijn zeer privileged en moeten regel matig worden geroteerd als een beveiligings best practice. Toegangs sleutels bevatten verbindings reeksen die referentie gegevens bevatten en worden beschouwd als geheimen. Deze geheimen moeten worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd bij Key Vault om ze op te halen. Toegangs sleutels kunnen lees-/schrijftoegang of alleen lees toegang tot een toepassing geven. Zorg ervoor dat het juiste type toegangs sleutel is uitgegeven om onbevoegde toegang te voor komen. Als u beter wilt beveiligen, gebruikt u de functie Managed Identities in azure AD. Hiervoor moeten alleen toepassingen de URL van het configuratie-eind punt hebben om toegang te krijgen tot configuratie waarden.

- [Aanbevolen procedures voor app-configuratie](howto-best-practices.md#app-configuration-bootstrap)

- [Beheerde identiteiten gebruiken om App Configuration te openen](howto-integrate-azure-managed-service-identity.md)
- [Machtigingen voor beheerdersrollen in Azure AD](../active-directory/roles/permissions-reference.md)

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../active-directory/roles/security-planning.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Richt lijnen**: bij Azure-app configuratie wordt gebruikgemaakt van Azure RBAC om de toegang tot bedrijfskritische systemen te isoleren door te beperken welke accounts bevoegde toegang krijgen. Azure RBAC wordt ondersteund door app-configuratie op het niveau van de resource. Als u bedrijfs kritieke configuraties veilig wilt opslaan, slaat u deze informatie op in een eigen app-configuratie bron. Binnen een resource is nauw keurige toegang beschikbaar via alleen-lezen toegangs accounts of-sleutels, evenals labels en labels.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [RBAC integreren met Azure AD met behulp van app-configuratie](concept-enable-rbac.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig beoordelen en afstemmen

**Richt lijnen**: bij de configuratie van Azure-app wordt gebruikgemaakt van Azure Active Directory (Azure AD)-accounts voor het beheren van de resources, de gebruikers accounts en toegangs toewijzingen regel matig controleren om ervoor te zorgen dat de accounts en hun toegang geldig zijn. 

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de configuratie bron van de app

U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen, zoals de bovenstaande app-configuratie rollen, te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Opmerking: beheerde identiteiten worden waar mogelijk gesuggereerd voor verificatie van de app-configuratie van een andere service of toepassing. U moet alle service-principals of verbindings reeksen die zijn geconfigureerd met toegang tot de app-configuratie, afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](/azure/active-directory/governance/access-reviews-overvie)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md)

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken die betrekking hebben op de app-configuratie. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: Azure-app configuratie is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor Azure-app configuratie en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:
- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is. 

App-configuratie biedt ondersteuning voor het opslaan van de configuratie van meerdere toepassingen in één app-configuratie bron. Als u de toegang tot informatie tussen toepassingen wilt beperken, maakt u een app-configuratie bron per toepassing en stelt u Azure RBAC dienovereenkomstig in.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)

- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning  

**Hulp**: Implementeer een goedkeurings proces voor de organisatie voor ondersteunings Scenario's waarbij micro soft mogelijk toegang nodig heeft tot uw app-configuratie gegevens. Klanten-lockbox is momenteel niet beschikbaar voor ondersteunings scenario's voor app-configuratie.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Ga voor meer informatie naar [Azure Security-benchmark: Gegevensbeveiliging](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: gevoelige gegevens detecteren, classificeren en labelen

**Richt lijnen**: uw gevoelige gegevens detecteren, classificeren en labelen zodat u de juiste controles kunt ontwerpen om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologie systemen van de organisatie. Het labelen voor gevoelige informatie, in de vorm van labels, wordt ondersteund voor app-configuratie resources, maar niet voor configuratie waarden die erin zijn opgenomen. Zodra een toepassing Lees-of lees-en schrijf toegang tot een configuratie opslag heeft, heeft deze volledige toegang tot een van de configuraties in dat archief.

- [Gevoelige gegevens labelen met Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Gegevens classificaties coderen in azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beveiligen

**Richt lijnen**: voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd. Zorg ervoor dat u de toegangs sleutels regel matig naar uw app-configuratie bronnen draait. Referentie gegevens van verbindings reeksen kunnen worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd bij Key Vault om ze op te halen. Toegangs sleutels kunnen lees-/schrijftoegang of alleen lees toegang tot een toepassing geven. Zorg ervoor dat het juiste type toegangs sleutel is uitgegeven om onbevoegde toegang te voor komen. Als u beter wilt beveiligen, gebruikt u de functie Managed Identities in azure AD. Hiervoor moeten alleen toepassingen de URL van het configuratie-eind punt hebben om toegang te krijgen tot configuratie waarden.

Toegang beperken met behulp van toegangs beheer op basis van rollen (Azure RBAC) van Azure:

- Los gevoelige gegevens op in een eigen app-configuratie bron en wijs vervolgens RBAC-beleid toe, zodat alleen gemachtigde toegang is ingeschakeld 

- Toegangs beheer op basis van het netwerk gebruiken

- Specifieke besturings elementen in Azure-Services (zoals versleuteling in SQL en andere data bases) en zorgen voor consistent toegangs beheer, alle typen toegangs beheer moeten worden afgestemd op uw strategie voor bedrijfs segmentatie.

- De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory](concept-enable-rbac.md)

- [Versleuteling van app-configuratie gegevens](faq.md#does-app-configuration-encrypt-my-data)

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: als u een aanvulling op toegangs controles wilt uitvoeren, moeten de gegevens in transit worden beschermd tegen buiten-band-aanvallen met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure-app configuratie maakt gebruik van TLS-versleuteling voor alle HTTP-aanvragen. De Azure-infra structuur biedt een extra laag voor het versleutelen van het netwerk niveau voor alle aanvragen tussen Azure-data centers. Zorg ervoor dat het HTTP-verkeer dat clients die verbinding maken met uw app-configuratie bronnen kunnen onderhandelen over TLS v 1.2 of hoger.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Gevoelige data-at-rest versleutelen

**Richt lijnen**: als u een aanvulling op toegangs controles wilt uitvoeren, moeten de gegevens in rust worden beschermd tegen buiten-band-aanvallen (zoals toegang tot onderliggende opslag) met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure biedt standaard versleuteling voor gegevens op rest. Voor zeer gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling in rust op alle Azure-resources waar beschikbaar. Azure beheert standaard uw versleutelings sleutels, maar Azure biedt de mogelijkheid om uw eigen sleutels te beheren (door de klant beheerde sleutels) voor Azure-app configuratie.

- [Door de klant beheerde sleutels gebruiken voor het versleutelen van uw gegevens in Azure-app configuratie](concept-customer-managed-keys.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Versleutelings model en sleutel beheer tabel](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Gegevens bij dubbele versleuteling in de rest van Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="asset-management"></a>Asset-management

*Ga voor meer informatie naar [Azure Security-benchmark: Asset-management](../security/benchmarks/security-controls-v2-asset-management.md).*

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

**Richt lijnen**: Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets op Azure, zoals Azure-app configuratie. Beveiligings teams hebben deze inventaris vaak nodig om de potentiële bloot stelling van hun organisatie aan opkomende Risico's te evalueren, en als een invoer voor voortdurend verbeterde beveiligings verbeteringen. Maak een Azure Active Directory groep die het gemachtigde beveiligings team van uw organisatie bevat en wijs hen Lees toegang toe aan alle Azure-app configuratie resources. Dit kan worden vereenvoudigd door één roltoewijzing op hoog niveau binnen uw abonnement.

Met de functie voor Azure Security Center Inventory en Azure resource Graph kunt u alle resources in uw abonnementen opvragen en detecteren, inclusief Azure-Services,-toepassingen en-netwerk bronnen.

Pas Tags toe op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: Azure-app configuratie ondersteunt implementaties op basis van Azure Resource Manager en het afdwingen van configuraties met behulp van Azure Policy. Gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richtlijnen**: Er moet beveiligingsbeleid worden ingesteld of bijgewerkt om te controleren op aanpassingen met mogelijk grote gevolgen van processen voor levenscyclusbeheer. Deze wijzigingen omvatten wijzigingen, maar zijn niet beperkt tot: id-providers en toegang, gegevens gevoeligheid, netwerk configuratie en toewijzing van Administrator bevoegdheden.

Verwijder Azure-resources wanneer deze ze niet meer nodig zijn. Zorg ervoor dat beheerders regel matig toegangs sleutels draaien om ervoor te zorgen dat alleen geverifieerde gebruikers toegang hebben tot hun configuratie bron.

- [Versleutelings sleutels voor toepassings configuratie draaien](concept-customer-managed-keys.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en bedreigingsdetectie

*Ga voor meer informatie naar [Azure Security-benchmark: Logboekregistratie en bedreigingsdetectie](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Hulp**: app-configuratie kan worden geïntegreerd met Azure Active Directory (Azure AD). Dit biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor, Azure Sentinel of andere hulpprogram ma's voor SIEM/controle voor geavanceerde bewakings-en analyse cases:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kan de bedreigings beveiligings module van Azure Security Center ook meer uitgebreide beveiligings waarschuwingen van Azure-service lagen verzamelen. Deze functie geeft u inzicht in de account afwijkingen binnen de afzonderlijke resources.

Een andere methode voor het verkrijgen van toegang tot de configuratie bron van de configuratie van de app is het gebruik van toegangs sleutels. Deze moeten regel matig worden gedraaid om te voor komen dat niet-geautoriseerde agents toegang krijgen tot uw configuratie bron. U kunt ze ook rechtstreeks in de portal uitvoeren onder toegangs toetsen.

- [Azure-app configuratie toestaan om toegang te krijgen tot andere met Azure AD beveiligde resources met behulp van een beheerde identiteit](overview-managed-identity.md)

- [Beheerde identiteiten gebruiken met Azure-app configuratie](howto-integrate-azure-managed-service-identity.md)

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Toegang tot Azure-app configuratie autoriseren met behulp van Azure AD](concept-enable-rbac.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Hulp**: de Azure-app-configuratie implementeert geen resources rechtstreeks in een virtueel netwerk. Met app-configuratie kunt u echter privé-eind punten gebruiken om veilig verbinding te maken met Azure-app configuratie van een virtueel netwerk. Azure-app-configuratie produceert ook geen DNS-query logboeken die moeten worden ingeschakeld.

Logboek registratie inschakelen voor uw geconfigureerde persoonlijke eind punten van de app-configuratie voor het vastleggen van:
- Gegevens verwerkt door het privé-eindpunt (IN/UIT)
- Gegevens verwerkt door de Private Link-service (IN/UIT)
- Beschikbaarheid NAT-poort

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Controle van persoonlijke Azure-koppelingen](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw app-configuratie resources, behalve Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd. Voor de configuratie van de app zijn activiteiten Logboeken alleen beschikbaar op het besturings element en worden ze geoppereerd door de Azure Resource Manager (ARM). De logboek registratie van de klant tegenover het gegevens vlak voor de app-configuratie wordt momenteel niet ondersteund. Azure-resource logboeken zijn ook niet beschikbaar om te worden geconfigureerd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden. Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaar periode voor logboek opslag configureren

**Richt lijnen**: Zorg ervoor dat voor opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van app-configuratie Logboeken de Bewaar periode voor logboeken is ingesteld volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie.

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/platform/manage-cost-storage.md)

- [Bron logboeken opslaan in een Azure Storage-account](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Ga voor meer informatie naar [Azure Security-benchmark: Reageren op incidenten](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding – proces voor reageren op incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat er processen aanwezig zijn in uw organisatie om te reageren op beveiligingsincidenten, dat deze processen zijn bijgewerkt voor Azure en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging implementeren binnen de onderneming](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor reageren op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding - melding van incidenten configureren

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

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

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

*Ga voor meer informatie naar [Azure Security-benchmark: Beheer van beveiligingspostuur en beveiligingsproblemen](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: beveiligde configuraties instellen voor Azure-Services 

**Hulp**: de configuratie van Azure-app ondersteunt onder servicespecifieke beleid dat beschikbaar is in azure Security Center om configuraties van uw Azure-resources te controleren en af te dwingen. Dit kan worden geconfigureerd in Azure Security Center-of Azure Policy-initiatieven.
- App-configuratie moet een door de klant beheerde sleutel gebruiken: door de klant beheerde sleutels bieden uitgebreide gegevens beveiliging door u toe te staan uw versleutelings sleutels te beheren. Dit is vaak nodig om aan de nalevingsvereisten te voldoen.
- App-configuratie moet een persoonlijke koppeling gebruiken: verbindingen met een particulier eind punt bieden clients in een virtueel netwerk veilige toegang tot Azure-app configuratie via een privé-koppeling.

U kunt Azure-blauw drukken gebruiken om de implementatie en configuratie van services en toepassings omgevingen te automatiseren, waaronder Azure Resources Manager-sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk-definitie.

- [Meer informatie over app-configuratie beleidsregels](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Werken met beveiligings beleid in Azure Security Center](../security-center/tutorial-security-policy.md)

- [Afbeelding van de implementatie van Guardrails in de lands zone van de Enter prise Scale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: beveiligde configuraties voor Azure-Services

**Hulp**: gebruik Azure Security Center om uw configuratie basislijn te bewaken en af te dwingen met behulp van Azure Policy. Azure Policy voor app-configuratie omvat het volgende: 
- App-configuratie moet een door de klant beheerde sleutel gebruiken: door de klant beheerde sleutels bieden uitgebreide gegevens beveiliging door u toe te staan uw versleutelings sleutels te beheren. Dit is vaak nodig om aan de nalevingsvereisten te voldoen.
- App-configuratie moet een persoonlijke koppeling gebruiken: verbindingen met een particulier eind punt bieden clients in een virtueel netwerk veilige toegang tot Azure-app configuratie via een privé-koppeling.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md) 

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmatig aanvalssimulaties uitvoeren

**Richtlijnen**: Voer zo nodig penetratietests of Red Teaming-activiteiten uit voor uw Azure-resources en zorg ervoor dat alle beveiligingsproblemen worden opgelost.
Volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests voldoen aan de beleidsregels van Microsoft. Volg de strategie en instructies van Microsoft voor Red Teaming en penetratietests van live sites voor cloudinfrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietests in Azure](../security/fundamentals/pen-testing.md)

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Bench Mark: Backup and Recovery](../security/benchmarks/security-controls-v2-backup-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: risico op verloren sleutels beperken

**Richt lijnen**: Zorg ervoor dat u maat regelen hebt om te voor komen en te herstellen van het verlies van sleutels. Schakel de beveiliging van zacht verwijderen en opschonen in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Zacht verwijderen en beveiliging opschonen inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Ga voor meer informatie naar [Azure Security-benchmark: Governance en strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Azure data security and encryption best practices](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-benchmark-v2-data-protection)

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

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-benchmark-v2-network-security)

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

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure identity management security overview](../security/fundamentals/identity-management-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons bij bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Stel prioriteiten om analisten te voorzien van waarschuwingen van hoge kwaliteit en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en handmatige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van het SecOps-team van de organisatie 

-   Een goed omschreven responsproces voor incidenten dat is afgestemd op NIST of een ander framework uit de branche 

-   Logboeken registreren en bewaren voor ondersteuning van bedreigingsdetectie, respons op incidenten en nalevingsbehoeften

-   Gecentraliseerde zichtbaarheid van en correlatiegegevens over bedreigingen, met behulp van SIEM, functies van Azure en andere bronnen 

-   Plan voor communicatie en melding aan klanten, leveranciers en openbare betrokken partijen

-   Gebruik van platforms van Azure en van derden voor het afhandelen van incidenten, zoals logboekregistratie en bedreigingsdetectie, forensics, en herstel en eliminatie van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijsbehoud

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Lees [dit overzichtsartikel over Azure Security Benchmark V2](../security/benchmarks/overview.md).
- Lees meer over [basislijnen voor de beveiliging van Azure](../security/benchmarks/security-baselines-overview.md)