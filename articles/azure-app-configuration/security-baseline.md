---
title: Azure-beveiligings basislijn voor Azure-app configuratie
description: De beveiligings basislijn van Azure-app configuratie bevat procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4d74c3607610372fa6cd276dd0d72fa5ba37f391
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025753"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure-beveiligings basislijn voor Azure-app configuratie

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Azure-app configuratie. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure-app configuratie. **Besturings elementen** die niet van toepassing zijn op Azure-app configuratie, zijn uitgesloten.

Zie voor meer informatie over hoe Azure-app configuratie volledig is toegewezen aan de beveiligings benchmark van Azure, het [volledige Azure-app configuratie beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp**: de Azure-app-configuratie implementeert geen resources rechtstreeks in een virtueel netwerk. Omdat de service niet is geïmplementeerd in een virtueel netwerk, kunt u bepaalde netwerk functies niet gebruiken om het interne verkeer van de service te beveiligen, zoals: netwerk beveiligings groepen, route tabellen of andere netwerk apparaten, zoals een Azure Firewall. Met app-configuratie kunt u echter privé-eind punten gebruiken om veilig verbinding te maken met Azure-app configuratie van een virtueel netwerk.

Gebruik Azure Sentinel om het gebruik van verouderde onveilige protocollen, zoals SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niet-ondertekende LDAP-bindingen en zwakke versleuteling in Kerberos, te detecteren.

- [Privé-eind punten gebruiken voor Azure-app configuratie](concept-private-endpoint.md)

- [Azure Sentinel Inveilige protocollen werkmap](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Hulp**: de configuratie van Azure-app biedt ondersteuning voor het gebruik van privé-eind punten voor het veilig verzenden van gegevens via een privé-koppeling. Gebruik Azure ExpressRoute of het virtuele particuliere netwerk (VPN) van Azure om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en Azure ExpressRoute.

Als u twee of meer virtuele netwerken in azure met elkaar wilt verbinden, gebruikt u peering op virtueel netwerk. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../expressroute/expressroute-connectivity-models.md)

- [Overzicht van Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering op virtueel netwerk](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: toegang tot privé-netwerk tot stand brengen met Azure-Services

**Hulp**: persoonlijke Azure-koppeling gebruiken om persoonlijke toegang tot Azure-app configuratie vanuit uw virtuele netwerken mogelijk te maken zonder het Internet te passeren.

Persoonlijke toegang is een extra verdedigings maatregel naast verificatie en verkeers beveiliging die wordt aangeboden door Azure-Services.

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Een persoonlijke koppeling instellen in Azure-app configuratie](concept-private-endpoint.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Richt lijnen**: wanneer u configuratie waarden via een virtueel netwerk opent, moet u uw resources beschermen tegen aanvallen van externe netwerken, waaronder DDoS-aanvallen (Distributed Denial of service), toepassingsspecifieke aanvallen, en ongevraagde en mogelijk schadelijk Internet verkeer. Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. Bescherm uw assets tegen DDoS-aanvallen door DDoS Standard-beveiliging in te scha kelen op uw virtuele Azure-netwerken. Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die betrekking hebben op uw netwerkgerelateerde bronnen.

Azure-app configuratie niet is bedoeld voor het uitvoeren van webtoepassingen, wordt de configuratie voor deze webtoepassingen geboden. U hoeft geen aanvullende instellingen te configureren of extra netwerk services te implementeren om deze te beveiligen tegen aanvallen van buitenaf die gericht zijn op webtoepassingen.

- [Documentatie over Azure Firewall](/azure/firewall/)

- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: gebruik Azure Firewall met filtering op basis van bedreigings informatie om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. Wanneer Payload inspectie vereist is, kunt u een oplossing van een derde partij/IP-adressen implementeren vanuit Azure Marketplace met Payload-inspectie mogelijkheden. U kunt er ook voor kiezen om op host gebaseerde ID'S/IP-adressen te gebruiken, of een EDR-oplossing (op basis van een host) in combi natie met of in plaats van op netwerk gebaseerde ID'S/IP-adressen.

Opmerking: als u een wettelijke of andere vereiste hebt voor het gebruik van ID'S/IP-adressen, moet u ervoor zorgen dat deze altijd is afgestemd op waarschuwingen van hoge kwaliteit voor uw SIEM-oplossing.

- [Azure Firewall implementeren](/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure Marketplace bevat mogelijkheden voor externe ID'S van derden](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Micro soft Defender ATP EDR-mogelijkheid](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: Azure Virtual Network Service-Tags gebruiken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall geconfigureerd voor uw app-configuratie resources. U kunt de service label ' AppConfiguration ' gebruiken in plaats van specifieke IP-adressen bij het maken van beveiligings regels voor uitgaand verkeer in het netwerk van uw toepassing. Door de servicetag naam op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Bench Mark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)(Engelstalig) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Chat-1: Azure Active Directory standaardiseren als het centrale identiteits-en verificatie systeem

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory (Azure AD) die de standaard service voor identiteits-en toegangs beheer van Azure is. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:
- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.
- De resources van uw organisatie, zoals toepassingen op Azure of uw bedrijfs netwerk resources.

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de Cloud beveiligings procedure van uw organisatie. Azure AD biedt een beveiligde score voor identiteiten om u te helpen bij het beoordelen van identiteits beveiliging postuur ten opzichte van de aanbevelingen van micro soft best practice. Gebruik de score om te meten hoe nauw keurig uw configuratie overeenkomt met best practice aanbevelingen en om verbeteringen aan te brengen in uw beveiligings postuur.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Inzender: gebruik deze rol om de configuratie bron van de app te beheren. Terwijl de app-configuratie gegevens kunnen worden geopend met behulp van toegangs sleutels, verleent deze rol geen directe toegang tot de gegevens met behulp van Azure AD.

- Lezer: gebruik deze rol om Lees toegang te verlenen aan de configuratie bron van de app. Hiermee wordt geen toegang verleend tot de toegangs sleutels van de resource en evenmin op de gegevens die zijn opgeslagen in de app-configuratie.

Zie voor meer informatie de volgende verwijzingen:

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Wat is de identiteit van beveiligde scores in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

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

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: in azure-app configuratie wordt gebruikgemaakt van Azure Active Directory (Azure AD) om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemings identiteiten zoals werk nemers, evenals externe identiteiten, zoals partners, leveranciers en leveranciers. Met Azure AD kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren van de app-configuratie service via de Azure Portal met behulp van een gesynchroniseerde bedrijfs Active Directory-identiteiten. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zicht baarheid en controle.

- [Meer informatie over de SSO van de toepassing met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>Chat-4: gebruik sterke verificatie-instellingen voor alle toegang op basis van Azure Active Directory

**Hulp**: in azure-app configuratie wordt gebruikgemaakt van Azure Active Directory die ondersteuning biedt voor krachtige verificatie-instellingen via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.
- Multi-factor Authentication: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor een aantal aanbevolen procedures in de MFA-installatie. MFA kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.
- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar: Windows hello voor bedrijven, Microsoft Authenticator-app en on-premises verificatie methoden zoals Smart Cards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

Opmerking: MFA kan worden afgedwongen voor de gebruikers accounts die toegang hebben tot de app-configuratie en deze beheren, maar niet op programmatische service accounts. Gebruik verificatie zonder wacht woord, zoals beheerde identiteiten waar mogelijk, en dwing MFA af voor elk gebruikers account.

- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Inleiding tot verificatie opties met een wacht woord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: controleren en waarschuwen voor afwijkingen van het account

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden geleverd:

-   Aanmeldingen: het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

-   Audit logboeken: voorziet in traceer baarheid via Logboeken voor alle wijzigingen die zijn aangebracht via verschillende functies in azure AD. Voor beelden van audit Logboeken in vastgelegde wijzigingen zijn het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleids regels.

-   Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

-   Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. 

Azure Advanced Threat Protection (ATP) is een beveiligings oplossing die on-premises Active Directory signalen kan gebruiken om geavanceerde bedreigingen, gemanipuleerde identiteiten en schadelijke Insider-acties te identificeren, te detecteren en te onderzoeken.

- [Activiteiten rapporten controleren in azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Risk ante aanmeldingen voor Azure AD weer geven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Waarschuwingen in de module Threat Intelligence-beveiliging van Azure Security Center](../security-center/alerts-reference.md)

- [Azure-activiteiten logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Verbinding maken met gegevens van Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Richt lijnen**: Azure-app configuratie ondersteunt de voorwaardelijke toegang van Azure Active Directory (Azure AD) voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken, moet MFA gebruiken voor aanmelding. Gedetailleerd beheer beleid voor verificatie sessies kan ook worden gebruikt voor verschillende use cases. Deze beleids regels voor voorwaardelijke toegang zijn alleen van toepassing op gebruikers accounts die worden geverifieerd bij Azure AD voor toegang tot en beheer van de app-configuratie service, maar ze zijn niet van toepassing op service-principals of verbindings reeksen die verbinding maken met uw configuratie bron.

- [Overzicht van voorwaardelijke toegang voor Azure](../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Verificatie sessie beheer met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Chat-7: onbedoelde referentie blootstelling elimineren

**Hulp**: met de Azure-app configuratie kunnen klanten configuraties opslaan die mogelijk identiteiten of geheimen bevatten. Het is raadzaam om referentie scanner te implementeren om referenties in configuraties te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Gebruik de Azure-app Configuration-service samen met Azure Key Vault. Sla referenties op in Key Vault en koppel deze referenties aan de hand van een Key Vault verwijzing in uw app-configuratie bron. Wanneer de app-configuratie deze verwijzingen maakt, worden de Uri's van de Key Vault waarden opgeslagen in plaats van de waarden zelf. Toepassingen kunnen verbinding maken met de app-configuratie om referenties op te halen uit Key Vault.

Voor GitHub kunt u de systeem eigen functie voor het scannen van geheime gegevens gebruiken om referenties of een andere vorm van geheimen binnen de code te identificeren.

- [Zelf studie voor het gebruik van Key Vault verwijzingen in een ASP.NET Core-app](use-key-vault-references-dotnet-core.md)

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub Secret Scanning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="privileged-access"></a>Privileged Access

*Zie [Azure Security Bench Mark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)(Engelstalig) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: gebruikers met hoge bevoegdheden beveiligen en beperken

**Richt lijnen**: Beperk het aantal accounts of rollen met veel bevoegdheden en beveilig deze accounts op een verhoogd niveau, omdat gebruikers met deze bevoegdheid elke resource in uw Azure-omgeving direct of indirect kunnen lezen en wijzigen.

U kunt met behulp van Azure AD Privileged Identity Management (PIM) just-in-time-toegang verlenen tot Azure-resources en Azure AD. JIT verleent tijdelijke machtigingen voor het uitvoeren van geprivilegieerde taken alleen wanneer gebruikers deze nodig hebben. PIM kan ook beveiligings waarschuwingen genereren wanneer er verdachte of onveilige activiteiten in uw Azure AD-organisatie zijn.

Toegangs sleutels zijn zeer privileged en moeten regel matig worden geroteerd als een beveiligings best practice. Toegangs sleutels bevatten verbindings reeksen die referentie gegevens bevatten en worden beschouwd als geheimen. Deze geheimen moeten worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd bij Key Vault om ze op te halen. Toegangs sleutels kunnen lees-/schrijftoegang of alleen lees toegang tot een toepassing geven. Zorg ervoor dat het juiste type toegangs sleutel is uitgegeven om onbevoegde toegang te voor komen. Als u beter wilt beveiligen, gebruikt u de functie Managed Identities in azure AD. Hiervoor moeten alleen toepassingen de URL van het configuratie-eind punt hebben om toegang te krijgen tot configuratie waarden.

- [Aanbevolen procedures voor app-configuratie](howto-best-practices.md#app-configuration-bootstrap)

- [Beheerde identiteiten gebruiken om App Configuration te openen](howto-integrate-azure-managed-service-identity.md)
- [Machtigingen voor beheerdersrol in azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Beveiligings waarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Richt lijnen**: bij Azure-app configuratie wordt gebruikgemaakt van Azure RBAC om de toegang tot bedrijfskritische systemen te isoleren door te beperken welke accounts bevoegde toegang krijgen. Azure RBAC wordt ondersteund door app-configuratie op het niveau van de resource. Als u bedrijfs kritieke configuraties veilig wilt opslaan, slaat u deze informatie op in een eigen app-configuratie bron. Binnen een resource is nauw keurige toegang beschikbaar via alleen-lezen toegangs accounts of-sleutels, evenals labels en labels.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [RBAC integreren met Azure AD met behulp van app-configuratie](concept-enable-rbac.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: gebruikers toegang regel matig controleren en afstemmen

**Richt lijnen**: bij de configuratie van Azure-app wordt gebruikgemaakt van Azure Active Directory (Azure AD)-accounts voor het beheren van de resources, de gebruikers accounts en toegangs toewijzingen regel matig controleren om ervoor te zorgen dat de accounts en hun toegang geldig zijn. 

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de configuratie bron van de app

U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen, zoals de bovenstaande app-configuratie rollen, te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Opmerking: beheerde identiteiten worden waar mogelijk gesuggereerd voor verificatie van de app-configuratie van een andere service of toepassing. U moet alle service-principals of verbindings reeksen die zijn geconfigureerd met toegang tot de app-configuratie, afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](/azure/active-directory/governance/access-reviews-overvie)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: Azure-app configuratie is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](/azure/active-directory/governance/access-reviews-overview)

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken die betrekking hebben op de app-configuratie. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Volg gewoon voldoende beheer (minimale bevoegdheids methode) 

**Hulp**: Azure-app configuratie is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor Azure-app configuratie en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. De bevoegdheden die u toewijst aan resources via Azure RBAC, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de just-in-time (JIT) benadering van Azure AD Privileged Identity Management (PIM) en moet regel matig worden gecontroleerd.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:
- Eigenaar van app-configuratie gegevens: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- Gegevens lezer app-configuratie: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is. 

App-configuratie biedt ondersteuning voor het opslaan van de configuratie van meerdere toepassingen in één app-configuratie bron. Als u de toegang tot informatie tussen toepassingen wilt beperken, maakt u een app-configuratie bron per toepassing en stelt u Azure RBAC dienovereenkomstig in.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)

- [RBAC configureren in azure](../role-based-access-control/role-assignments-portal.md)

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](/azure/active-directory/governance/access-reviews-overview)

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure AD](concept-enable-rbac.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning  

**Hulp**: Implementeer een goedkeurings proces voor de organisatie voor ondersteunings Scenario's waarbij micro soft mogelijk toegang nodig heeft tot uw app-configuratie gegevens. Klanten-lockbox is momenteel niet beschikbaar voor ondersteunings scenario's voor app-configuratie.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)voor meer informatie.*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: gevoelige gegevens detecteren, classificeren en labelen

**Richt lijnen**: uw gevoelige gegevens detecteren, classificeren en labelen zodat u de juiste controles kunt ontwerpen om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologie systemen van de organisatie. Het labelen voor gevoelige informatie, in de vorm van labels, wordt ondersteund voor app-configuratie resources, maar niet voor configuratie waarden die erin zijn opgenomen. Zodra een toepassing Lees-of lees-en schrijf toegang tot een configuratie opslag heeft, heeft deze volledige toegang tot een van de configuraties in dat archief.

- [Label gevoelige informatie met behulp van Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Gegevens classificaties coderen in azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="dp-2-protect-sensitive-data"></a>DP-2: gevoelige gegevens beveiligen

**Richt lijnen**: voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure beveiligd blijven, heeft micro soft enkele standaard besturings elementen voor gegevens bescherming geïmplementeerd. Zorg ervoor dat u de toegangs sleutels regel matig naar uw app-configuratie bronnen draait. Referentie gegevens van verbindings reeksen kunnen worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd bij Key Vault om ze op te halen. Toegangs sleutels kunnen lees-/schrijftoegang of alleen lees toegang tot een toepassing geven. Zorg ervoor dat het juiste type toegangs sleutel is uitgegeven om onbevoegde toegang te voor komen. Als u beter wilt beveiligen, gebruikt u de functie Managed Identities in azure AD. Hiervoor moeten alleen toepassingen de URL van het configuratie-eind punt hebben om toegang te krijgen tot configuratie waarden.

Toegang beperken met behulp van toegangs beheer op basis van rollen (Azure RBAC) van Azure:

- Los gevoelige gegevens op in een eigen app-configuratie bron en wijs vervolgens RBAC-beleid toe, zodat alleen gemachtigde toegang is ingeschakeld 

- Toegangs beheer op basis van het netwerk gebruiken

- Specifieke besturings elementen in Azure-Services (zoals versleuteling in SQL en andere data bases) en zorgen voor consistent toegangs beheer, alle typen toegangs beheer moeten worden afgestemd op uw strategie voor bedrijfs segmentatie.

- De strategie voor bedrijfs segmentatie moet ook worden geïnformeerd over de locatie van gevoelige of bedrijfs kritieke gegevens en systemen.

Zie voor meer informatie de volgende verwijzingen:

- [Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory](concept-enable-rbac.md)

- [Versleuteling van app-configuratie gegevens](faq.md#does-app-configuration-encrypt-my-data)

- [Access Control op basis van Azure Role (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: als u een aanvulling op toegangs controles wilt uitvoeren, moeten de gegevens in transit worden beschermd tegen buiten-band-aanvallen met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure-app configuratie maakt gebruik van TLS-versleuteling voor alle HTTP-aanvragen. De Azure-infra structuur biedt een extra laag voor het versleutelen van het netwerk niveau voor alle aanvragen tussen Azure-data centers. Zorg ervoor dat het HTTP-verkeer dat clients die verbinding maken met uw app-configuratie bronnen kunnen onderhandelen over TLS v 1.2 of hoger.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: gevoelige gegevens in rust versleutelen

**Richt lijnen**: als u een aanvulling op toegangs controles wilt uitvoeren, moeten de gegevens in rust worden beschermd tegen buiten-band-aanvallen (zoals toegang tot onderliggende opslag) met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure biedt standaard versleuteling voor gegevens op rest. Voor zeer gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling in rust op alle Azure-resources waar beschikbaar. Azure beheert standaard uw versleutelings sleutels, maar Azure biedt de mogelijkheid om uw eigen sleutels te beheren (door de klant beheerde sleutels) voor Azure-app configuratie.

- [Door de klant beheerde sleutels gebruiken voor het versleutelen van uw gegevens in Azure-app configuratie](concept-customer-managed-keys.md)

- [Meer informatie over versleuteling in de rest van Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Versleutelings model en sleutel beheer tabel](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Gegevens bij dubbele versleuteling in de rest van Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Bench Mark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: controleren of het beveiligings team inzicht heeft in Risico's voor assets

**Hulp**: Zorg ervoor dat beveiligings teams machtigingen voor beveiligings lezers krijgen in uw Azure-Tenant en-abonnementen zodat ze kunnen controleren op beveiligings Risico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Dat wil zeggen dat beveiligings inzichten en-risico's altijd centraal moeten worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Opmerking: er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van de rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure Beheergroepen](../governance/management-groups/overview.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets op Azure, zoals Azure-app configuratie. Beveiligings teams hebben deze inventaris vaak nodig om de potentiële bloot stelling van hun organisatie aan opkomende Risico's te evalueren, en als een invoer voor voortdurend verbeterde beveiligings verbeteringen. Maak een Azure Active Directory groep die het gemachtigde beveiligings team van uw organisatie bevat en wijs hen Lees toegang toe aan alle Azure-app configuratie resources. Dit kan worden vereenvoudigd door één roltoewijzing op hoog niveau binnen uw abonnement.

Met de functie voor Azure Security Center Inventory en Azure resource Graph kunt u alle resources in uw abonnementen opvragen en detecteren, inclusief Azure-Services,-toepassingen en-netwerk bronnen.

Pas Tags toe op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: Azure-app configuratie ondersteunt implementaties op basis van Azure Resource Manager en het afdwingen van configuraties met behulp van Azure Policy. Gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: zorg voor de beveiliging van levenscyclus beheer van middelen

**Hulp** bij het maken of bijwerken van beveiligings beleid voor het beheer van de levens duur van asset-processen voor mogelijk grote veranderingen. Deze wijzigingen omvatten wijzigingen, maar zijn niet beperkt tot: id-providers en toegang, gegevens gevoeligheid, netwerk configuratie en toewijzing van Administrator bevoegdheden.

Verwijder Azure-resources wanneer ze niet meer nodig zijn. Zorg ervoor dat beheerders regel matig toegangs sleutels draaien om ervoor te zorgen dat alleen geverifieerde gebruikers toegang hebben tot hun configuratie bron.

- [Versleutelings sleutels voor toepassings configuratie draaien](concept-customer-managed-keys.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-threat-detection"></a>Logboek registratie en detectie van bedreigingen

*Zie [Azure Security Bench Mark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)(Engelstalig) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: detectie van bedreigingen inschakelen voor Azure-identiteits-en toegangs beheer

**Hulp**: app-configuratie kan worden geïntegreerd met Azure Active Directory (Azure AD). Dit biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor, Azure Sentinel of andere hulpprogram ma's voor SIEM/controle voor geavanceerde bewakings-en analyse cases:
- Aanmeldingen: het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kunt ook waarschuwen voor bepaalde verdachte activiteiten, zoals overmatig aantal mislukte verificatie pogingen, afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne, kan de bedreigings beveiligings module van Azure Security Center ook meer uitgebreide beveiligings waarschuwingen van Azure-service lagen verzamelen. Deze functie geeft u inzicht in de account afwijkingen binnen de afzonderlijke resources.

Een andere methode voor het verkrijgen van toegang tot de configuratie bron van de configuratie van de app is het gebruik van toegangs sleutels. Deze moeten regel matig worden gedraaid om te voor komen dat niet-geautoriseerde agents toegang krijgen tot uw configuratie bron. U kunt ze ook rechtstreeks in de portal uitvoeren onder toegangs toetsen.

- [Azure-app configuratie toestaan om toegang te krijgen tot andere met Azure AD beveiligde resources met behulp van een beheerde identiteit](overview-managed-identity.md)

- [Beheerde identiteiten gebruiken met Azure-app configuratie](howto-integrate-azure-managed-service-identity.md)

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Toegang tot Azure-app configuratie autoriseren met behulp van Azure AD](concept-enable-rbac.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Hulp**: de Azure-app-configuratie implementeert geen resources rechtstreeks in een virtueel netwerk. Met app-configuratie kunt u echter privé-eind punten gebruiken om veilig verbinding te maken met Azure-app configuratie van een virtueel netwerk. Azure-app-configuratie produceert ook geen DNS-query logboeken die moeten worden ingeschakeld.

Logboek registratie inschakelen voor uw geconfigureerde persoonlijke eind punten van de app-configuratie voor het vastleggen van:
- Gegevens verwerkt door het privé-eindpunt (IN/UIT)
- Gegevens verwerkt door de Private Link-service (IN/UIT)
- Beschikbaarheid NAT-poort

Zie voor meer informatie de volgende verwijzingen:

- [Controle van persoonlijke Azure-koppelingen](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw app-configuratie resources, behalve Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd. Voor de configuratie van de app zijn activiteiten Logboeken alleen beschikbaar op het besturings element en worden ze geoppereerd door de Azure Resource Manager (ARM). De logboek registratie van de klant tegenover het gegevens vlak voor de app-configuratie wordt momenteel niet ondersteund. Azure-resource logboeken zijn ook niet beschikbaar om te worden geconfigureerd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden. Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaar periode voor logboek opslag configureren

**Richt lijnen**: Zorg ervoor dat voor opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van app-configuratie Logboeken de Bewaar periode voor logboeken is ingesteld volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie.

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/platform/manage-cost-storage.md)

- [Bron logboeken opslaan in een Azure Storage-account](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

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

Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

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

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: beveiligde configuraties voor Azure-Services

**Hulp**: gebruik Azure Security Center om uw configuratie basislijn te bewaken en af te dwingen met behulp van Azure Policy. Azure Policy voor app-configuratie omvat het volgende: 
- App-configuratie moet een door de klant beheerde sleutel gebruiken: door de klant beheerde sleutels bieden uitgebreide gegevens beveiliging door u toe te staan uw versleutelings sleutels te beheren. Dit is vaak nodig om aan de nalevingsvereisten te voldoen.
- App-configuratie moet een persoonlijke koppeling gebruiken: verbindingen met een particulier eind punt bieden clients in een virtueel netwerk veilige toegang tot Azure-app configuratie via een privé-koppeling.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md) 

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: regel matige aanvals simulatie uitvoeren

**Richt lijnen**: u kunt zo nodig indringings tests of rode team activiteiten uitvoeren op uw Azure-resources en ervoor zorgen dat alle essentiële beveiligings resultaten worden hersteld.
Volg de Microsoft Cloud regels voor het testen van de indringings fase om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Indringings tests in azure](../security/fundamentals/pen-testing.md)

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Bench Mark: Backup and Recovery](/azure/security/benchmarks/security-controls-v2-backup-recovery)(Engelstalig) voor meer informatie.*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: risico op verloren sleutels beperken

**Richt lijnen**: Zorg ervoor dat u maat regelen hebt om te voor komen en te herstellen van het verlies van sleutels. Schakel de beveiliging van zacht verwijderen en opschonen in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Zacht verwijderen en beveiliging opschonen inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

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

-   Gebruik van mogelijkheden van Azure native en gegevens bescherming van derden

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
