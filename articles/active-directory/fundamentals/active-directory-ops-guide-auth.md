---
title: Azure Active Directory naslaghandleiding voor bewerkingen voor verificatiebeheer
description: In deze referentiehandleiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om verificatiebeheer te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531948"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory naslaghandleiding voor bewerkingen voor verificatiebeheer

In deze sectie van de naslaghandleiding voor [Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om referenties te beveiligen en beheren, verificatie-ervaring te definiëren, toewijzing te delegeren, gebruik te meten en toegangsbeleid te definiëren op basis van de beveiligingsstatus van de onderneming.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen na een bepaalde periode worden gewijzigd. Organisaties moeten voortdurend hun identiteitsprocedures evalueren naarmate Microsoft-producten en -services zich in de loop van de tijd ontwikkelen.

## <a name="key-operational-processes"></a>Belangrijke operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigenaren toewijzen aan belangrijke taken

Voor Azure Active Directory is continue uitvoering van belangrijke operationele taken en processen vereist, die mogelijk geen deel uitmaken van een implementatieproject. Het is nog steeds belangrijk dat u deze taken in stelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigenaren zijn onder andere:

| Taak | Eigenaar |
| :- | :- |
| Levenscyclus van eenmalige aanmelding (SSO)-configuratie in Azure AD beheren | IAM Operations-team |
| Beleid voor voorwaardelijke toegang ontwerpen voor Azure AD-toepassingen | InfoSec-architectuurteam |
| Aanmeldingsactiviteiten archiveren in een SIEM-systeem | InfoSec Operations-team |
| Risicogebeurtenissen archiveren in een SIEM-systeem | InfoSec Operations-team |
| Beveiligingsrapporten triageen en onderzoeken | InfoSec Operations-team |
| Risicogebeurtenissen triageer en onderzoek | InfoSec Operations-team |
| Gebruikers die zijn gemarkeerd voor risico- en beveiligingsrapporten van gebruikers op Azure AD Identity Protection | InfoSec Operations-team |

> [!NOTE]
> Azure AD Identity Protection is een Azure AD Premium P2-licentie vereist. Zie [Comparing generally available features of the Azure AD Free and Azure AD Premium editions](https://azure.microsoft.com/pricing/details/active-directory/)(Algemeen beschikbare functies van de Azure AD Free en Azure AD Premium vergelijken) voor de juiste licentie voor uw vereisten.

Wanneer u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken die een eigenaar niet hebben of het eigendom aanpassen voor taken met eigenaren die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owner-recommended-reading"></a>Aanbevolen lees lezen door eigenaar

- [Beheerdersrollen toewijzen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Referentiebeheer

### <a name="password-policies"></a>Wachtwoordbeleid

Het veilig beheren van wachtwoorden is een van de belangrijkste onderdelen van identiteits- en toegangsbeheer en vaak het grootste doel van aanvallen. Azure AD ondersteunt verschillende functies die kunnen helpen voorkomen dat een aanval slaagt.

Gebruik de onderstaande tabel om de aanbevolen oplossing te vinden voor het oplossen van het probleem dat moet worden opgelost:

| Probleem | Aanbeveling |
| :- | :- |
| Geen mechanisme om te beveiligen tegen zwakke wachtwoorden | [Self-service voor wachtwoord opnieuw instellen (SSPR) en](../authentication/concept-sspr-howitworks.md) wachtwoordbeveiliging van Azure AD [inschakelen](../authentication/concept-password-ban-bad-on-premises.md) |
| Geen mechanisme om gelekte wachtwoorden te detecteren | [Wachtwoordhashsynchronisatie](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) inschakelen om inzichten te verkrijgen |
| Met AD FS en kan niet worden verplaatst naar beheerde verificatie | Smart [Lockout AD FS extranet en/of](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) Slimme vergrendeling [van Azure AD inschakelen](../authentication/howto-password-smart-lockout.md) |
| Wachtwoordbeleid maakt gebruik van op complexiteit gebaseerde regels, zoals lengte, sets met meerdere tekens of verloop | Ga na of aanbevolen [procedures van Microsoft](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) worden aanbevolen en schakel uw benadering voor wachtwoordbeheer over en [implementeer Azure AD-wachtwoordbeveiliging.](../authentication/concept-password-ban-bad.md) |
| Gebruikers zijn niet geregistreerd voor het gebruik van Multi-Factor Authentication (MFA) | [Registreer alle beveiligingsgegevens van de](../identity-protection/howto-identity-protection-configure-mfa-policy.md) gebruiker, zodat deze kan worden gebruikt als een mechanisme om de identiteit van de gebruiker en het wachtwoord te verifiëren |
| Wachtwoorden worden niet intrekken op basis van gebruikersrisico | Azure AD [Identity Protection-beleid voor gebruikersrisico's implementeren om](../identity-protection/howto-identity-protection-configure-risk-policies.md) wachtwoordwijzigingen af te dwingen voor gelekte referenties met behulp van SSPR |
| Er is geen slim vergrendelingsmechanisme om schadelijke verificatie te beschermen tegen schadelijke actoren die afkomstig zijn van geïdentificeerde IP-adressen | Door de cloud beheerde verificatie implementeren met wachtwoordhashsynchronisatie of [pass-through-verificatie](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Wachtwoordbeleid wordt aanbevolen om te lezen

- [Azure AD en AD FS best practices: Bescherming tegen wachtwoordsprayaanvallen - Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Selfservice voor wachtwoord opnieuw instellen en wachtwoordbeveiliging inschakelen

Gebruikers die hun wachtwoord moeten wijzigen of opnieuw moeten instellen, zijn een van de grootste bronnen van volume en kosten van helpdeskoproepen. Naast de kosten is het wijzigen van het wachtwoord als een hulpmiddel om een gebruikersrisico te beperken een fundamentele stap bij het verbeteren van het beveiligingspostuur van uw organisatie.

U wordt ten minste aangeraden azure AD [self-service](../authentication/concept-sspr-howitworks.md) voor wachtwoord opnieuw [](../authentication/howto-password-ban-bad-on-premises-deploy.md) instellen (SSPR) en on-premises wachtwoordbeveiliging te implementeren om het volgende te bereiken:

- Wijs helpdeskoproepen uit.
- Vervang het gebruik van tijdelijke wachtwoorden.
- Vervang alle bestaande selfservice voor wachtwoordbeheer die afhankelijk is van een on-premises oplossing.
- [Zwakke wachtwoorden in](../authentication/concept-password-ban-bad.md) uw organisatie elimineren.

> [!NOTE]
> Voor organisaties met een Azure AD Premium P2-abonnement is het raadzaam om SSPR te implementeren en te gebruiken als onderdeel van een beleid voor gebruikersrisico's voor [identiteitsbeveiliging.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="strong-credential-management"></a>Sterk referentiebeheer

Wachtwoorden zelf zijn niet veilig genoeg om te voorkomen dat kwaaddoeners toegang krijgen tot uw omgeving. Elke gebruiker met een bevoorrecht account moet minimaal zijn ingeschakeld voor Meervoudige verificatie (MFA). In het ideale moment moet u [gecombineerde registratie inschakelen en](../authentication/concept-registration-mfa-sspr-combined.md) vereisen dat alle gebruikers zich registreren voor MFA en SSPR met behulp van de [gecombineerde registratie-ervaring](../user-help/security-info-setup-signin.md). Uiteindelijk raden we u aan een strategie te kiezen om [tolerantie te bieden om](../authentication/concept-resilient-controls.md) het risico op vergrendeling als gevolg van onvoorziene omstandigheden te verminderen.

![Gecombineerde gebruikerservaringsstroom](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Tolerantie voor on-premises uitvalverificatie

Naast de voordelen van eenvoud en het inschakelen van detectie van gelekte referenties, bieden Azure AD Password Hash Sync (PHS) en Azure AD MFA gebruikers toegang tot SaaS-toepassingen en Microsoft 365 ondanks on-premises uitval vanwege cyberaanvallen zoals [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Het is ook mogelijk om PHS in teschakelen in combinatie met federatie. Als u PHS inschakelen, kunt u verificatie terugdraaien wanneer er geen federatieservices beschikbaar zijn.

Als uw on-premises organisatie geen tolerantiestrategie voor uitval heeft of een strategie heeft die niet is geïntegreerd met Azure AD, moet u Azure AD PHS implementeren en een noodherstelplan definiëren dat PHS bevat. Als u Azure AD PHS inschakelen, kunnen gebruikers zich verifiëren bij Azure AD als uw on-premises Active Directory niet beschikbaar is.

![synchronisatiestroom voor wachtwoordhash](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Zie Choose the right authentication method for your Azure Active Directory hybrid identity solution (De juiste verificatiemethode kiezen voor uw [hybride identiteitsoplossing) Azure Active Directory meer inzicht in uw verificatieopties.](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Programmatisch gebruik van referenties

Azure AD-scripts die gebruikmaken van PowerShell of toepassingen die gebruikmaken van de Microsoft Graph API vereisen beveiligde verificatie. Slecht referentiebeheer bij het uitvoeren van deze scripts en hulpprogramma's verhoogt het risico op referentiediefstal. Als u scripts of toepassingen gebruikt die afhankelijk zijn van in code gecodeerde wachtwoorden of wachtwoordprompts, moet u eerst wachtwoorden controleren in configuratiebestanden [](../reports-monitoring/tutorial-access-api-with-certificates.md) of broncode, deze afhankelijkheden vervangen en waar mogelijk Azure Managed Identities, Integrated-Windows Authentication of certificaten gebruiken. Voor toepassingen waarbij de vorige oplossingen niet mogelijk zijn, kunt u overwegen [om](https://azure.microsoft.com/services/key-vault/)Azure Key Vault.

Als u bepaalt dat er service-principals met wachtwoordreferenties zijn en u niet zeker weet hoe deze wachtwoordreferenties worden beveiligd door scripts of toepassingen, neem dan contact op met de eigenaar van de toepassing voor meer informatie over gebruikspatronen.

Microsoft raadt u ook aan contact op te nemen met toepassingseigenaren om gebruikspatronen te begrijpen als er service-principals met wachtwoordreferenties zijn.

## <a name="authentication-experience"></a>Verificatie-ervaring

### <a name="on-premises-authentication"></a>On-premises verificatie

Federatieverificatie met Geïntegreerde Windows-verificatie (IWA) of naadloze, door één Sign-On (SSO) beheerde verificatie met wachtwoordhashsynchronisatie of pass-through-verificatie is de beste gebruikerservaring in het bedrijfsnetwerk met line-of-sight voor on-premises domeincontrollers. Het minimaliseert de prompts voor referenties en vermindert het risico dat gebruikers te maken krijgen met phishing-aanvallen. Als u al cloudverificatie met PHS of PTA gebruikt, maar gebruikers nog steeds hun wachtwoord moeten invoeren bij de verificatie on-premises, moet u naadloze eenmalige aanmelding [onmiddellijk implementeren.](../hybrid/how-to-connect-sso.md) Als u daarentegen momenteel federatief bent met plannen om uiteindelijk te migreren naar door de cloud beheerde verificatie, moet u Naadloze eenmalige aanmelding implementeren als onderdeel van het migratieproject.

### <a name="device-trust-access-policies"></a>Toegangsbeleid voor apparaatvertrouwen

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beveiligen. U kunt de apparaat-id gebruiken om uw resources altijd en overal te beschermen. Door het apparaat te authenticeren en voor het vertrouwenstype te zorgen, verbetert u uw beveiligingsstatus en bruikbaarheid door:

- Voorkomen van frictie, bijvoorbeeld met MFA, wanneer het apparaat wordt vertrouwd
- Toegang vanaf niet-vertrouwde apparaten blokkeren
- Voor Windows 10 apparaten biedt u probleemloos een aanmelding [aan bij on-premises resources.](../devices/azuread-join-sso.md)

U kunt dit doel uitvoeren door apparaat-id's te gebruiken en deze te beheren in Azure AD met behulp van een van de volgende methoden:

- Organisaties kunnen met [Microsoft Intune](/intune/what-is-intune) apparaat beheren en nalevingsbeleid afdwingen, de status van apparaten bevestigen en beleid voor voorwaardelijke toegang instellen op basis van of het apparaat compatibel is. Microsoft Intune kunt iOS-apparaten, Mac-desktops (via JAMF-integratie), Windows-desktops (systeemeigen met Mobile Device Management voor Windows 10 en co-beheer met Microsoft Endpoint Configuration Manager) en mobiele Android-apparaten beheren.
- [Hybride Azure AD-join](../devices/hybrid-azuread-join-managed-domains.md) biedt beheer met groepsbeleidsregels of Microsoft Endpoint Configuration Manager in een omgeving met computers die lid zijn van een Active Directory-domein. Organisaties kunnen een beheerde omgeving implementeren via PHS of PTA met naadloze eenmalige aanmelding. Door uw apparaten naar Azure AD te brengen, wordt de productiviteit van gebruikers gemaximaliseerd via eenmalige [](../conditional-access/overview.md) aanmelding in uw cloud- en on-premises resources, terwijl u tegelijkertijd de toegang tot uw cloud- en on-premises resources kunt beveiligen met voorwaardelijke toegang.

Als u Windows-apparaten hebt die lid zijn van een domein en niet zijn geregistreerd in de cloud of Windows-apparaten die zijn geregistreerd in de cloud, maar zonder beleid voor voorwaardelijke toegang, moet u de niet-geregistreerde apparaten registreren en in beide gevallen hybride [Azure AD-join](../conditional-access/require-managed-devices.md) gebruiken als besturingselement in uw beleid voor voorwaardelijke toegang.

![Een schermopname van toekenning in beleid voor voorwaardelijke toegang waarvoor hybride apparaten zijn vereist](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Als u apparaten beheert met MDM of Microsoft Intune, maar geen apparaatbesturingselementen gebruikt [](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) in uw beleidsregels voor voorwaardelijke toegang, raden we u aan in dat beleid Vereisen dat het apparaat als compatibel is gemarkeerd als besturingselement te gebruiken.

![Een schermopname van toekenning in het beleid voor voorwaardelijke toegang waarvoor apparaat-naleving is vereist](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Aanbevolen leestoegangsbeleid voor apparaatvertrouwen

- [Procedure: de implementatie van uw hybride Azure Active Directory-koppeling plannen](../devices/hybrid-azuread-join-plan.md)
- [Configuraties voor identiteiten en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

In Windows 10 vervangt [Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification) wachtwoorden door sterke twee-factor authentication op pc's. Windows Hello voor Bedrijven een meer gestroomlijnde MFA-ervaring voor gebruikers en vermindert u uw afhankelijkheid van wachtwoorden. Als u nog niet bent begonnen met het Windows 10-apparaten of als u ze slechts gedeeltelijk hebt [](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) geïmplementeerd, raden we u aan om een upgrade uit te voeren naar Windows 10 en Windows Hello voor Bedrijven in te Windows Hello voor Bedrijven alle apparaten.

Zie A [world without passwords with Azure Active Directory (A world without passwords with Azure Active Directory](../authentication/concept-authentication-passwordless.md)( Een wereld zonder wachtwoorden met Azure Active Directory.

## <a name="application-authentication-and-assignment"></a>Toepassingsverificatie en -toewijzing

### <a name="single-sign-on-for-apps"></a>Een aanmelding voor apps

Het bieden van een gestandaardiseerd mechanisme voor een enkele aanmelding voor de hele onderneming is van cruciaal belang voor de beste gebruikerservaring, het verminderen van risico's, de mogelijkheid om te rapporteren en governance. Als u toepassingen gebruikt die ondersteuning bieden voor eenmalige aanmelding met Azure AD, maar momenteel zijn geconfigureerd voor het gebruik van lokale accounts, moet u deze toepassingen opnieuw configureren om eenmalige aanmelding met Azure AD te gebruiken. En als u toepassingen gebruikt die ondersteuning bieden voor eenmalige aanmelding met Azure AD, maar wel een andere id-provider gebruiken, moet u deze toepassingen ook opnieuw configureren om eenmalige aanmelding met Azure AD te gebruiken. Voor toepassingen die geen ondersteuning bieden voor federatieprotocollen maar wel verificatie op [](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) basis van formulieren ondersteunen, wordt u aangeraden de toepassing te configureren voor het gebruik van wachtwoordkluis met Azure AD-toepassingsproxy.

![Aanmelding op basis van een appproxy-wachtwoord](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Als u geen mechanisme hebt om niet-gebruikte toepassingen in uw organisatie te ontdekken, raden we u aan een detectieproces te implementeren met behulp van een CLOUD Access Security Broker-oplossing (CASB), zoals [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Als u ten slotte een Azure AD-app-galerie hebt en toepassingen gebruikt die ondersteuning bieden voor eenmalige aanmelding met Azure AD, raden we u aan de toepassing in de [app-galerie weer te geven.](../develop/v2-howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Aanbevolen lees lezen voor een een-op-een-aanmelding

- [Wat is toegang tot toepassingen en een aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migratie van AD FS naar Azure AD

[Het migreren van apps van AD FS naar Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) biedt extra mogelijkheden op het gebied van beveiliging, consistentere beheerbaarheid en een betere samenwerkingservaring. Als u toepassingen hebt geconfigureerd in AD FS die ondersteuning bieden voor eenmalige aanmelding met Azure AD, moet u deze toepassingen opnieuw configureren om eenmalige aanmelding met Azure AD te gebruiken. Als u toepassingen hebt geconfigureerd in AD FS met ongebruikelijke configuraties die niet worden ondersteund door Azure AD, moet u contact opnemen met de eigenaren van de app om te begrijpen of de speciale configuratie een absolute vereiste van de toepassing is. Als dit niet vereist is, moet u de toepassing opnieuw configureren voor het gebruik van eenmalige aanmelding met Azure AD.

![Azure AD als de primaire id-provider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health voor ADFS](../hybrid/how-to-connect-health-adfs.md) kunnen worden gebruikt voor het verzamelen van configuratiegegevens over elke toepassing die mogelijk kan worden gemigreerd naar Azure AD.

### <a name="assign-users-to-applications"></a>Gebruikers toewijzen aan toepassingen

[Gebruikers toewijzen aan toepassingen](../manage-apps/assign-user-or-group-access-portal.md) kan het beste worden toegewezen met behulp van groepen, omdat ze meer flexibiliteit en de mogelijkheid bieden om op schaal te beheren. De voordelen van het gebruik van groepen zijn [onder andere dynamisch](../enterprise-users/groups-dynamic-membership.md) groepslidmaatschap op basis van kenmerken en overdracht aan [app-eigenaren.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Als u groepen al gebruikt en beheert, raden we u daarom aan de volgende acties uit te voeren om het beheer op schaal te verbeteren:

- Groepsbeheer en governance delegeren aan toepassingseigenaren.
- Selfservicetoegang tot de toepassing toestaan.
- Definieer dynamische groepen als gebruikerskenmerken consistent de toegang tot toepassingen kunnen bepalen.
- Attestation implementeren voor groepen die worden gebruikt voor toegang tot toepassingen met [behulp van Azure AD-toegangsbeoordelingen.](../governance/access-reviews-overview.md)

Als u daarentegen toepassingen vindt die zijn toewijsen aan afzonderlijke gebruikers, moet u [governance implementeren](../governance/index.yml) rond deze toepassingen.

#### <a name="assign-users-to-applications-recommended-reading"></a>Gebruikers toewijzen aan toepassingen die worden aanbevolen te lezen

- [Gebruikers en groepen toewijzen aan een toepassing in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [App-registratiemachtigingen delegeren in Azure Active Directory](../roles/delegate-app-roles.md)
- [Dynamische lidmaatschapsregels voor groepen in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Toegangsbeleid

### <a name="named-locations"></a>Benoemde locaties

Met [benoemde locaties](../reports-monitoring/quickstart-configure-named-locations.md) in Azure AD kunt u vertrouwde IP-adresbereiken in uw organisatie labelen. Azure AD maakt gebruik van benoemde locaties voor:

- Fout-positieven voorkomen in risicogebeurtenissen. Aanmelden vanaf een vertrouwde netwerklocatie verlaagt het aanmeldingsrisico van een gebruiker.
- Configureren van [Voorwaardelijke toegang op basis van locatie](../reports-monitoring/quickstart-configure-named-locations.md).

![Benoemde locatie](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Gebruik op basis van prioriteit de onderstaande tabel om de aanbevolen oplossing te vinden die het beste voldoet aan de behoeften van uw organisatie:

| **Priority** | **Scenario** | **Aanbeveling** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Als u PHS of PTA gebruikt en benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 2 | Als u federatief bent en geen gebruik maakt van de claim 'insideCorporateNetwork' en benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 3 | Als u geen benoemde locaties gebruikt in het beleid voor voorwaardelijke toegang en er geen risico of apparaatbesturingselementen zijn in het beleid voor voorwaardelijke toegang | Het beleid voor voorwaardelijke toegang configureren om benoemde locaties op te nemen |
| 4 | Als u federatief bent en wel de claim 'insideCorporateNetwork' gebruikt en benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 5 | Als u vertrouwde IP-adressen gebruikt met MFA in plaats van benoemde locaties en deze markeert als vertrouwd | Benoemde locaties definiëren en markeren als vertrouwd om de detectie van risicogebeurtenissen te verbeteren |

### <a name="risk-based-access-policies"></a>Op risico gebaseerd toegangsbeleid

Azure AD kan het risico voor elke aanmelding en elke gebruiker berekenen. Het gebruik van risico's als criterium in toegangsbeleid kan een betere gebruikerservaring bieden, bijvoorbeeld minder verificatieprompts en betere beveiliging, bijvoorbeeld alleen gebruikers vragen wanneer ze nodig zijn, en het antwoord en herstel automatiseren.

![Beleid voor aanmeldingsrisico's](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Als u al eigenaar bent Azure AD Premium P2-licenties die ondersteuning bieden voor het gebruik van risico's in toegangsbeleid, maar ze niet worden gebruikt, raden we u ten zeerste aan om risico's toe te voegen aan uw beveiligingsstatus.

#### <a name="risk-based-access-policies-recommended-reading"></a>Aanbevolen leestoegangsbeleid op basis van risico

- [Hoe: Het beleid voor aanmeldingsrisico's configureren](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Hoe: Het beleid voor gebruikersrisico's configureren](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Toegangsbeleid voor clienttoepassing

Microsoft Intune Application Management (MAM) biedt de mogelijkheid om besturingselementen voor gegevensbeveiliging, zoals opslagversleuteling, pincode, opschonen van externe opslag, enzovoort, te pushen naar compatibele mobiele clienttoepassingen zoals Outlook Mobile. Daarnaast kan beleid voor voorwaardelijke [](../conditional-access/app-based-conditional-access.md) toegang worden gemaakt om de toegang tot cloudservices zoals Exchange Online vanuit goedgekeurde of compatibele apps te beperken.

Als uw werknemers mam-toepassingen installeren, zoals mobiele Office-apps, voor toegang tot bedrijfsresources zoals Exchange Online of SharePoint Online, en u ook BYOD (Bring Your Own Device) ondersteunt, raden we u aan om MAM-beleid voor toepassingen te implementeren om de toepassingsconfiguratie te beheren op apparaten in persoonlijk eigendom zonder MDM-inschrijving en vervolgens uw beleid voor voorwaardelijke toegang bij te werken zodat alleen toegang wordt toegestaan vanaf clients die geschikt zijn voor MAM.

![Besturingselement voor voorwaardelijke toegang verlenen](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Als werknemers MAM-toepassingen installeren op bedrijfsresources en de toegang wordt beperkt op door Intune beheerde apparaten, kunt u overwegen MAM-beleid voor toepassingen te implementeren voor het beheren van de toepassingsconfiguratie voor persoonlijke apparaten en het beleid voor voorwaardelijke toegang bij te werken om alleen toegang toe te staan vanaf clients die geschikt zijn voor MAM.

### <a name="conditional-access-implementation"></a>Implementatie van voorwaardelijke toegang

Voorwaardelijke toegang is een essentieel hulpmiddel voor het verbeteren van de beveiligingsstatus van uw organisatie. Daarom is het belangrijk dat u deze best practices volgt:

- Zorg ervoor dat op alle SaaS-toepassingen ten minste één beleid is toegepast
- Vermijd het combineren van **Alle apps** filter met het **blokbesturingselement** om vergrendelingsrisico's te voorkomen
- Vermijd het gebruik **van alle gebruikers** als filter en voeg per ongeluk gasten **toe**
- **Alle verouderde beleidsregels migreren naar de Azure Portal**
- Alle criteria voor gebruikers, apparaten en toepassingen ondervangen
- Beleid voor voorwaardelijke toegang gebruiken [om MFA te implementeren](../conditional-access/plan-conditional-access.md)in plaats van een **MFA per gebruiker te gebruiken**
- Een kleine set kernbeleidsregels hebben die van toepassing kunnen zijn op meerdere toepassingen
- Lege uitzonderingsgroepen definiëren en deze toevoegen aan het beleid om een uitzonderingsstrategie te hebben
- Plan voor [break glass-accounts](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) zonder MFA-besturingselementen
- Zorg voor een consistente ervaring Microsoft 365 clienttoepassingen, bijvoorbeeld Teams, OneDrive, Outlook, enzovoort) door dezelfde set besturingselementen te implementeren voor services zoals Exchange Online en Sharepoint Online
- Toewijzing aan beleidsregels moet worden geïmplementeerd via groepen, niet via personen
- Controleer regelmatig de uitzonderingsgroepen die worden gebruikt in beleid om de tijd te beperken die gebruikers buiten het beveiligings postuur hebben. Als u eigenaar bent van Azure AD P2, kunt u toegangsbeoordelingen gebruiken om het proces te automatiseren

#### <a name="conditional-access-recommended-reading"></a>Aanbevolen leestoegang voor voorwaardelijke toegang

- [Best practices voor voorwaardelijke toegang in Azure Active Directory](../conditional-access/overview.md)
- [Configuraties voor identiteiten en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory naslag voor instellingen voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-conditions.md)
- [Algemeen beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Toegang surface area

### <a name="legacy-authentication"></a>Verouderde verificatie

Sterke referenties, zoals MFA, kunnen apps niet beveiligen met verouderde verificatieprotocollen, waardoor het de voorkeursaanvalsvector wordt door kwaadwillende actoren. Het vergrendelen van verouderde verificatie is van cruciaal belang voor het verbeteren van de beveiligingsstatus van toegang.

Verouderde verificatie is een term die verwijst naar verificatieprotocollen die worden gebruikt door apps zoals:

- Oudere Office-clients die geen moderne verificatie gebruiken (bijvoorbeeld Office 2010-client)
- Clients die gebruikmaken van e-mailprotocollen zoals IMAP/SMTP/POP

Aanvallers geven de voorkeur aan deze protocollen. In feite maakt bijna [100%](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) van de aanvallen op wachtwoordsprays gebruik van verouderde verificatieprotocollen. Hackers gebruiken verouderde verificatieprotocollen, omdat ze geen ondersteuning bieden voor interactieve aanmelding, wat nodig is voor extra beveiligingsproblemen, zoals meervoudige verificatie en apparaatverificatie.

Als verouderde verificatie veel wordt gebruikt in uw omgeving, moet [](/office365/enterprise/modern-auth-for-office-2013-and-2016) u van plan zijn om verouderde clients zo snel mogelijk te migreren naar clients die moderne verificatie ondersteunen. Als u in hetzelfde token al gebruikers hebt die gebruikmaken van moderne verificatie, maar andere gebruikers die nog steeds gebruikmaken van verouderde verificatie, moet u de volgende stappen ondernemen om verouderde verificatie-clients te vergrendelen:

1. Gebruik [rapporten voor aanmeldingsactiviteiten om](../reports-monitoring/concept-sign-ins.md) gebruikers te identificeren die nog gebruikmaken van verouderde verificatie en herstelplannen:

   a. Upgraden naar clients die geschikt zijn voor moderne verificatie naar betrokken gebruikers.
   
   b. Plan een cutover-tijdsbestek om de onderstaande stappen te vergrendelen.
   
   c. Identificeer welke oudere toepassingen een harde afhankelijkheid hebben van verouderde verificatie. Zie stap 3 hieronder.

2. Schakel verouderde protocollen uit bij de bron (bijvoorbeeld Exchange-postvak) voor gebruikers die geen verouderde auth gebruiken om meer blootstelling te voorkomen.
3. Gebruik voor de resterende accounts (idealiter niet-menselijke identiteiten zoals serviceaccounts) voorwaardelijke toegang om na verificatie verouderde [protocollen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) te beperken.

#### <a name="legacy-authentication-recommended-reading"></a>Verouderde verificatie aanbevolen lezen

- [POP3- of IMAP4-toegang tot postvakken in Exchange Server in- of uitschakelen](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Toestemming verlenen

Bij een aanval op onrechtmatige toestemmingsverlening maakt de aanvaller een bij Azure AD geregistreerde toepassing die toegang vraagt tot gegevens, zoals contactgegevens, e-mail of documenten. Gebruikers kunnen via phishing-aanvallen toestemming geven voor schadelijke toepassingen wanneer ze op schadelijke websites landen.

Hieronder vindt u een lijst met apps met machtigingen die u mogelijk wilt onderzoeken voor Microsoft-cloudservices:

- Apps met een app of \* gedelegeerde . ReadWrite-machtigingen
- Apps met gedelegeerde machtigingen kunnen e-mail lezen, verzenden of beheren namens de gebruiker
- Apps die aan worden verleend met behulp van de volgende machtigingen:

| Resource | Machtiging |
| :- | :- |
| Exchange Online | Eas. AccessAsUser.All |
| | EWS. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Apps die volledige gebruikers-imitatie van de aangemelde gebruiker hebben gekregen. Bijvoorbeeld:

|Resource | Machtiging |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST-API | user_impersonation |

Om dit scenario te voorkomen, moet u verwijzen naar het detecteren en herstellen van onrechtmatige toestemmingsaanvragen [in Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) om toepassingen met onrechtmatige toekenningen of toepassingen te identificeren en te herstellen die meer toekenningen hebben dan nodig is. Verwijder vervolgens [de selfservice helemaal en](../manage-apps/configure-user-consent.md) stel [governanceprocedures in.](../manage-apps/configure-admin-consent-workflow.md) Plan ten slotte regelmatige beoordelingen van app-machtigingen en verwijder deze wanneer ze niet nodig zijn.

#### <a name="consent-grants-recommended-reading"></a>Toestemming verleent aanbevolen lees lezen

- [Microsoft Graph API-machtigingen](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Gebruikers- en groepsinstellingen

Hieronder vindt u de gebruikers- en groepsinstellingen die kunnen worden vergrendeld als er geen expliciete zakelijke behoefte is:

#### <a name="user-settings"></a>Gebruikersinstellingen

- **Externe gebruikers:** externe samenwerking kan organisch plaatsvinden in de onderneming met services zoals Teams, Power BI, Sharepoint Online en Azure Information Protection. Als u expliciete beperkingen hebt voor het beheren van door de gebruiker geïnitieerde externe samenwerking, wordt u aangeraden externe gebruikers in te stellen met behulp van [Azure AD-rechtenbeheer](../governance/entitlement-management-overview.md) of een gecontroleerde bewerking, zoals via de helpdesk. Als u geen organische externe samenwerking voor services wilt toestaan, kunt u blokkeren dat leden externe [gebruikers volledig uitnodigen.](../external-identities/delegate-invitations.md) U kunt ook specifieke domeinen toestaan of [blokkeren](../external-identities/allow-deny-list.md) in externe gebruikersuitnodigingen.
- **App-registraties:** wanneer App-registraties ingeschakeld, kunnen eindgebruikers zelf toepassingen onboarden en toegang verlenen tot hun gegevens. Een typisch voorbeeld van app-registratie is dat gebruikers Outlook-in plug-ins of spraakassistenten, zoals Siri, in staat stellen hun e-mail en agenda te lezen of namens hen e-mailberichten te verzenden. Als de klant besluit app-registratie uit te schakelen, moeten de InfoSec- en IAM-teams betrokken zijn bij het beheer van uitzonderingen (app-registraties die nodig zijn op basis van zakelijke vereisten), omdat ze de toepassingen moeten registreren met een beheerdersaccount en waarschijnlijk een proces moeten ontwerpen om het proces operationeel te maken.
- **Beheerportal:** organisaties kunnen de Azure AD-blade in de Azure Portal vergrendelen, zodat niet-beheerders geen toegang hebben tot Azure AD-beheer in de Azure Portal en in de war raken. Ga naar de gebruikersinstellingen in de Azure AD-beheerportal om de toegang te beperken:

![Beperkte toegang tot de beheerportal](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Niet-beheerders hebben nog steeds toegang tot de Azure AD-beheerinterfaces via opdrachtregelinterfaces en andere programmatische interfaces.

#### <a name="group-settings"></a>Groepsinstellingen

**Selfservice voor groepsbeheer/gebruikers kunnen beveiligingsgroepen/Microsoft 365 maken.** Als er op dit moment geen selfservice-initiatief voor groepen in de cloud is, kunnen klanten besluiten dit uit te schakelen totdat ze klaar zijn om deze mogelijkheid te gebruiken.

#### <a name="groups-recommended-reading"></a>Aanbevolen leesgroepen

- [Wat is Azure Active Directory B2B-samenwerking?](../external-identities/what-is-b2b.md)
- [Toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md)
- [Apps, machtigingen en toestemming in Azure Active Directory.](../develop/quickstart-register-app.md)
- [Groepen gebruiken voor het beheren van de toegang tot resources in Azure Active Directory](./active-directory-manage-groups.md)
- [Selfservice voor toegangsbeheer voor toepassingen instellen in Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Verkeer van onverwachte locaties

Aanvallers zijn afkomstig uit verschillende delen van de wereld. Beheer dit risico door beleid voor voorwaardelijke toegang te gebruiken met locatie als voorwaarde. Met [de locatievoorwaarde](../conditional-access/location-condition.md) van een beleid voor voorwaardelijke toegang kunt u de toegang blokkeren voor locaties waar geen zakelijke reden is om u aan te melden.

![Een nieuwe benoemde locatie maken](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Gebruik, indien beschikbaar, een SIEM-oplossing (Security Information and Event Management) om toegangspatronen tussen regio's te analyseren en te vinden. Als u geen SIEM-product gebruikt of als er geen verificatiegegevens van Azure AD worden opgenomen, raden we u aan om Azure Monitor te gebruiken om toegangspatronen tussen regio's [te](../../azure-monitor/overview.md) identificeren.

## <a name="access-usage"></a>Toegang tot gebruik

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-logboeken die zijn gearchiveerd en geïntegreerd met plannen voor het reageren op incidenten

Toegang tot aanmeldingsactiviteiten, controles en risicogebeurtenissen voor Azure AD is van cruciaal belang voor probleemoplossing, gebruiksanalyse en forensisch onderzoek. Azure AD biedt toegang tot deze bronnen via REST API's met een beperkte retentieperiode. Een SIEM-systeem (Security Information and Event Management), of gelijkwaardige archiveringstechnologie, is essentieel voor langetermijnopslag van controles en ondersteuning. Als u langetermijnopslag van Azure AD-logboeken wilt inschakelen, moet u deze toevoegen aan uw bestaande SIEM-oplossing of gebruikmaken [van Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Archiveer logboeken die kunnen worden gebruikt als onderdeel van uw plannen en onderzoeken voor het reageren op incidenten.

#### <a name="logs-recommended-reading"></a>Logboeken die worden aanbevolen te lezen

- [Azure Active Directory audit-API-verwijzing](/graph/api/resources/directoryaudit)
- [Azure Active Directory API-verwijzing voor aanmeldingsactiviteitenrapport](/graph/api/resources/signin)
- [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph voor Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 Management Activity API-verwijzing](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI Content Pack gebruiken](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Samenvatting

Er zijn 12 aspecten van een beveiligde identiteitsinfrastructuur. Met deze lijst kunt u referenties verder beveiligen en beheren, verificatie-ervaring definiëren, toewijzing delegeren, gebruik meten en toegangsbeleid definiëren op basis van de beveiligingsstatus van de onderneming.

- Wijs eigenaren toe aan belangrijke taken.
- Oplossingen implementeren om zwakke of gelekte wachtwoorden te detecteren, wachtwoordbeheer en -beveiliging te verbeteren en gebruikerstoegang tot resources verder te beveiligen.
- Beheer de identiteit van apparaten om uw resources op elk moment en vanaf elke locatie te beveiligen.
- Implementeert verificatie zonder wachtwoord.
- Een gestandaardiseerd mechanisme voor een enkele aanmelding bieden in de hele organisatie.
- Apps migreren van AD FS naar Azure AD voor betere beveiliging en consistente beheerbaarheid.
- Wijs gebruikers toe aan toepassingen met behulp van groepen om meer flexibiliteit en de mogelijkheid te bieden om op schaal te beheren.
- Op risico gebaseerd toegangsbeleid configureren.
- Verouderde verificatieprotocollen vergrendelen.
- Onrechtmatige toestemmings verleent detecteren en herstellen.
- Vergrendel gebruikers- en groepsinstellingen.
- Langetermijnopslag van Azure AD-logboeken inschakelen voor probleemoplossing, gebruiksanalyse en forensisch onderzoek.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [operationele controles en acties voor identiteitsbeheer.](active-directory-ops-guide-govern.md)
