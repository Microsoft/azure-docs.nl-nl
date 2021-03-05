---
title: Wat is nieuw? Release opmerkingen-Azure Active Directory | Microsoft Docs
description: Meer informatie over wat er nieuw is in Azure Active Directory; zoals de nieuwste opmerkingen bij de release, bekende problemen, fout oplossingen, afgeschafte functionaliteit en aanstaande wijzigingen.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/4/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9abed17f5a3d23f811c7cec0d4fd31e4433f651d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177013"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>U kunt een melding krijgen wanneer deze pagina is bijgewerkt door de URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` te kopiëren en plakken in uw ![pictogram van RSS-feedlezer](./media/whats-new/feed-icon-16x16.png) feedlezer.

Azure AD ontvangt voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt. Ga daarom regel matig opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in [Archief voor wat er nieuw is in azure Active Directory](whats-new-archive.md).

---
## <a name="february-2021"></a>Februari 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Authenticatie van verificatie met eenmalige wacht woord op de standaard datum van oktober 2021

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

Vanaf 31 oktober 2021 wordt de verificatie van de [eenmalige wachtwoord code](../external-identities/one-time-passcode.md) voor Microsoft Azure Active Directory e-mail de standaard methode voor het uitnodigen van accounts en tenants voor B2B-samenwerkings scenario's. Op dit moment kunnen door micro soft geen uitnodigingen meer worden uitgewisseld met niet-beheerde Azure Active Directory accounts. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Niet-aangevraagde maar toegestuurde machtigingen worden niet meer toegevoegd aan tokens als ze voorwaardelijke toegang zouden activeren

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Onafhankelijk
 
Op dit moment worden toepassingen die gebruikmaken van [dynamische machtigingen](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) alle machtigingen gegeven die ze hebben gezonden. Dit geldt ook voor toepassingen die niet zijn aangevraagd, zelfs als ze voorwaardelijke toegang activeren. Dit kan bijvoorbeeld ertoe leiden dat een app die alleen een aanvraag heeft ingediend waarvoor `user.read` ook toestemming is `files.read` gegeven, wordt geforceerd dat de voorwaardelijke toegang wordt toegewezen aan de `files.read` machtiging. 

Azure AD wijzigt de manier waarop niet-aangevraagde scopes worden geleverd aan toepassingen om het aantal onnodige voorwaardelijke toegangs prompts te verminderen. Apps activeren alleen voorwaardelijke toegang voor machtigingen die ze expliciet hebben aangevraagd. Lees [Wat is er nieuw in authenticatie](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)voor meer informatie.
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Open bare Preview: een tijdelijke toegangs fase gebruiken om referenties voor een wacht woord te registreren

**Type:** Nieuwe functie  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De tijdelijke toegangs fase is een tijdgebonden wachtwoord code die als sterke referenties fungeert en de onboarding van wacht woordloze referenties en herstel toestaat wanneer een gebruiker de sterke authenticatie factor (bijvoorbeeld FIDO2 of Microsoft Authenticator) heeft kwijt geraakt of is verg eten, en moet zich aanmelden om nieuwe krachtige authenticatie methoden te registreren. [Meer informatie](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Open bare preview-hand ingelogd blijven (KMSI) in de volgende generatie van gebruikers stromen

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

De volgende generatie van B2C-gebruikers stromen ondersteunt nu de [KMSI-functionaliteit (keep me aangemeld)](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) waarmee klanten de duur van de sessie kunnen uitbreiden voor de gebruikers van hun web-en systeem eigen toepassingen met behulp van een permanente cookie.  functie houdt de sessie actief, zelfs wanneer de gebruiker de browser sluit en opnieuw opent, en wordt ingetrokken wanneer de gebruiker zich afmeldt.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Open bare preview-externe identiteiten Self-Service aanmelden in AAD met MSA-accounts

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Externe gebruikers kunnen nu micro soft-accounts gebruiken om zich aan te melden bij Azure AD, de eerste partij en LOB-apps. [Meer informatie](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Open bare preview-status voor een gast gebruiker opnieuw instellen

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Klanten kunnen nu bestaande externe gast gebruikers uitnodigen om hun aflossings status opnieuw in te stellen, zodat de gast gebruikers account kan blijven werken zonder dat ze daar toegang toe hebben. [Meer informatie](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Open bare preview-Api's van/synchronization (inrichting) ondersteunen nu toepassings machtigingen

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Klanten kunnen nu Application. readwrite. ownedby als een toepassings machtiging gebruiken om de synchronisatie-Api's aan te roepen. Houd er rekening mee dat dit alleen wordt ondersteund voor het inrichten van Azure AD in toepassingen van derden (bijvoorbeeld AWS, gegevens Bricks, enzovoort). Het wordt momenteel niet ondersteund voor HR-inrichting (workday/Successfactors) of Cloud synchronisatie (AD naar Azure AD). [Meer informatie](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Algemene Beschik baarheid: ingebouwde rol verificatie beleids beheerder

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Gebruikers met deze rol kunnen het beleid voor verificatie methoden, MFA-instellingen voor tenants en wachtwoord beveiligings beleid configureren. Met deze rol wordt een machtiging verleend voor het beheren van instellingen voor wachtwoord beveiliging: slimme vergrendelings configuraties en het bijwerken van de lijst met aangepaste verboden wacht woorden. [Meer informatie](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Algemene Beschik baarheid: gebruikers verzamelingen in mijn apps zijn nu beschikbaar.

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Gebruikers kunnen nu hun eigen groeperingen van apps maken op het start programma voor apps van mijn toepassingen. Ze kunnen ook verzamelingen die met hen zijn gedeeld, opnieuw ordenen en verbergen door hun beheerder. [Meer informatie](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Algemene Beschik baarheid-automatisch door voeren in verificator

**Type:** Nieuwe functie  
**Service categorie:** App Microsoft Authenticator  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Microsoft Authenticator biedt mogelijkheden voor multi-factor Authentication (MFA) en account beheer, en u kunt nu ook de wacht woorden voor sites en apps die gebruikers bezoeken op hun mobiele apparaten (iOS en Android) door voeren. 

Als u automatisch invullen wilt gebruiken op verificator, moeten gebruikers hun persoonlijke Microsoft-account toevoegen aan de verificator en gebruiken om hun wacht woord te synchroniseren. Werk-of school accounts kunnen op dit moment niet worden gebruikt voor het synchroniseren van wacht woorden. [Meer informatie](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Algemene Beschik baarheid-interne gebruikers uitnodigen voor B2B-samen werking

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Klanten kunnen nu interne gasten uitnodigen om B2B-samen werking te gebruiken in plaats van een uitnodiging naar een bestaand intern account te verzenden. Hierdoor kunnen klanten de object-ID, UPN, groepslid maatschappen en app-toewijzingen van die gebruiker blijven gebruiken. [Meer informatie](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Algemene Beschik baarheid-ingebouwde rol domein naam beheerder

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Gebruikers met deze rol kunnen domein namen beheren (lezen, toevoegen, verifiëren, bijwerken en verwijderen). Ze kunnen ook Directory gegevens over gebruikers, groepen en toepassingen lezen, aangezien deze objecten domein afhankelijkheden hebben. 

Voor on-premises omgevingen kunnen gebruikers met deze rol domein namen voor federatie configureren zodat gekoppelde gebruikers altijd on-premises worden geverifieerd. Deze gebruikers kunnen zich vervolgens aanmelden bij Azure AD-Services met hun on-premises wacht woorden via eenmalige aanmelding. Federatie-instellingen moeten worden gesynchroniseerd via Azure AD Connect, dus gebruikers hebben ook machtigingen voor het beheren van Azure AD Connect. [Meer informatie](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-februari 2021

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In februari 2021 zijn de volgende 37 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Lussen van de Mess enger-uitbrei ding](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD-adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus pre-en onboarding platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold-account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-th +](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IOT-brug](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), opnieuw [kijken](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot-Car Park Management](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty semantisch Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox en Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis, flexibel platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SaaS](../saas-apps/olfeo-saas-tutorial.md), [Sigma computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox-beheer platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), Klaxoon [SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

U kunt ook de documentatie van alle toepassingen hier vinden: https://aka.ms/AppsTutorial

Lees de volgende informatie voor het weer geven van uw toepassing in de app-galerie van Azure AD: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-februari 2021

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Lees voor meer informatie [Gebruikers inrichten automatiseren voor SaaS-toepassingen met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Algemene Beschik baarheid-10 Azure Active Directory rollen nu hernoemd

**Type:** Gewijzigde functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
10 ingebouwde rollen van Azure AD zijn hernoemd zodat ze zijn afgestemd op de [Microsoft 365 beheer centrum](https://docs.microsoft.com/microsoft-365/admin/microsoft-365-admin-center-preview), de [Azure AD-portal](https://portal.azure.com/)en de [Microsoft Graph](https://developer.microsoft.com/graph/). Raadpleeg [beheerders rollen machtigingen in azure Active Directory](../roles/permissions-reference.md#all-roles)voor meer informatie over de nieuwe rollen.

![Tabel met nieuwe rolnaams](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nieuwe bedrijfs huisstijl in MFA/SSPR gecombineerde registratie

**Type:** Gewijzigde functie  
**Service categorie:** Gebruikers ervaring en-beheer  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
In het verleden zijn bedrijfs logo's niet gebruikt op Azure Active Directory aanmeldings pagina's. De huis stijl van het bedrijf bevindt zich nu in de linkerbovenhoek van MFA/SSPR gecombineerde registratie. De huis stijl van het bedrijf is ook opgenomen op mijn Sign-Ins en de pagina met beveiligings gegevens. [Meer informatie](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Algemene Beschik baarheid-Manager op tweede niveau kan worden ingesteld als alternatieve goed keurder

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Een extra optie wanneer u goed keurders selecteert, is nu beschikbaar in het recht beheer. Als u voor de eerste goed keurder ' Manager als fiatteur ' selecteert, beschikt u over een andere optie: ' tweede niveau Manager als alternatieve goed keurder ', beschikbaar om te kiezen in het veld alternatieve goed keurder. Als u deze optie selecteert, moet u een terugval-fiatteur toevoegen om de aanvraag door te sturen naar wanneer het systeem de tweede niveau Manager niet kan vinden. [Meer informatie](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Activiteiten dashboard voor verificatie methoden

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 

Het dash board activiteit met de vernieuwde verificatie methoden biedt beheerders een overzicht van de registratie van de verificatie methode en de gebruiks activiteit in hun Tenant. Het rapport geeft een overzicht van het aantal gebruikers dat voor elke methode is geregistreerd en de methoden die worden gebruikt tijdens het aanmelden en het opnieuw instellen van het wacht woord. [Meer informatie](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>De configuratie van de levens duur van de tokens voor vernieuwen en sessies in de Configureer bare levens duur (CTL) wordt buiten gebruik gesteld

**Type:** Keur  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Gebruikers verificatie
 
De configuratie van de levens duur van de vernieuwings-en sessie tokens in de CTL worden ingetrokken. Azure Active Directory voldoet niet meer aan de configuratie van vernieuwen en sessie token in bestaande beleids regels. [Meer informatie](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Januari 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Geheim token is een verplicht veld bij het configureren van inrichting

**Type:** Plan voor wijziging  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

In het verleden zou het veld geheim kunnen worden bewaard bij het instellen van de inrichting van de aangepaste/BYOA toepassing. Deze functie is uitsluitend bedoeld om te worden gebruikt voor het testen. De gebruikers interface wordt bijgewerkt om het veld in te stellen op vereist. 

Klanten kunnen deze vereiste omzeilen voor test doeleinden door een functie vlag te gebruiken in de browser-URL. [Meer informatie](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Open bare Preview: gedeelde Android-apparaten aanpassen en configureren voor Firstline-werk rollen op schaal

**Type:** Nieuwe functie  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Azure AD en micro soft Endpoint Manager-teams hebben gecombineerd om de mogelijkheid te bieden om uw Firstline-werk apparaten aan te passen, te schalen en te beveiligen.

Met de volgende preview-functies kunt u:
- Gedeelde Android-apparaten op schaal inrichten met micro soft Endpoint Manager
- Uw toegang voor Shift-werk nemers beveiligen met voorwaardelijke toegang op basis van een apparaat
- Aanmeld ervaringen aanpassen voor de ploegen werk rollen met het beheerde Start scherm

Zie voor meer informatie over het [aanpassen en configureren van gedeelde apparaten voor Firstline-werk rollen op schaal](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Open bare preview-logboeken kunnen nu worden gedownload als CSV-of JSON-bestand

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Klanten kunnen de inrichtings logboeken downloaden als een CSV-of JSON-bestand via de gebruikers interface en via Graph API. Raadpleeg [het inrichtings rapporten in de Azure Active Directory-Portal](../reports-monitoring/concept-provisioning-logs.md)voor meer informatie.

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Open bare preview-Cloud groepen toewijzen aan aangepaste Azure AD-rollen en rollen met beheerders eenheden

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Klanten kunnen een Cloud groep toewijzen aan aangepaste Azure AD-rollen of een rol voor beheerders eenheden. Raadpleeg [Cloud groepen gebruiken om roltoewijzingen in azure Active Directory te beheren](../roles/groups-concept.md)voor meer informatie over het gebruik van deze functie.

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Algemene Beschik baarheid-Azure AD Connect Cloud synchronisatie (voorheen bekend als Cloud inrichting)

**Type:** Nieuwe functie  
**Service categorie:** Cloud synchronisatie Azure AD Connect  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Azure AD Connect Cloud synchronisatie is nu algemeen beschikbaar voor alle klanten.

Azure AD Connect-Cloud verplaatst het grote aantal transformatie logica naar de Cloud, waardoor uw on-premises footprint worden verminderd. Daarnaast zijn er meerdere onderweginge agent implementaties beschikbaar voor een hogere synchronisatie Beschik baarheid. [Meer informatie](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Algemene Beschik baarheid-simulatie van het simuleren van een aanval met een aanval

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Er zijn twee nieuwe functies in Role-Based Access Control beschikbaar om toe te wijzen aan gebruikers, simulatie van de beheerder van de aanval of de schrijver van de aanval. 

Gebruikers in de rol [simulatie van aanvals beheerder](../roles/permissions-reference.md#attack-simulation-administrator) hebben toegang tot alle simulaties in de Tenant en kunnen:
- alle aspecten van het maken van aanvals simulaties maken en beheren
- starten/plannen van een simulatie
-  Bekijk simulatie resultaten. 

Gebruikers met de rol voor het schrijven van een [aanvals lading](../roles/permissions-reference.md#attack-payload-author) kunnen een Payload voor aanvallen maken, maar niet daad werkelijk starten of plannen. Er zijn vervolgens nettoladingen voor aanvallen beschikbaar voor alle beheerders in de Tenant die ze kunnen gebruiken om een simulatie te maken.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Algemene Beschik baarheid-samen vatting van gebruiks overzicht rapporten ingebouwde rol

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Gebruikers met de rol rapport lezers gebruiks overzicht hebben toegang tot geaggregeerde gegevens op Tenant niveau en gerelateerde inzichten in Microsoft 365-beheer centrum voor gebruiks-en productiviteits scores. Ze hebben echter geen toegang tot details of inzichten op gebruikers niveau. 

In het Microsoft 365-beheer centrum voor de twee rapporten maken we onderscheid tussen geaggregeerde gegevens op Tenant niveau en Details van gebruikers niveau. Deze rol voegt een extra beveiligingslaag toe aan afzonderlijke door de gebruiker herken bare gegevens. [Meer informatie](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Algemene Beschik baarheid: toekenning van het beveiligings beleid voor apps vereisen in voorwaardelijke toegang voor Azure AD

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De toekenning van voorwaardelijke toegang voor Azure AD voor ' beleid voor app-beveiliging vereisen ' is nu beschikbaar. 

Het beleid biedt de volgende mogelijkheden:
- Alleen toegang toestaan wanneer u een mobiele toepassing gebruikt die ondersteuning biedt voor intune-app-beveiliging
- Hiermee wordt alleen toegang gegeven wanneer een gebruiker een intune-app-beveiligings beleid voor de mobiele toepassing heeft ontvangen

Meer informatie over het instellen van een beleid voor voorwaardelijke toegang voor app- [beveiliging.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Algemene Beschik baarheid-wachtwoord code van E-mail One-Time

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Met e-mail-OTP kunnen organisaties over de hele wereld samen werken met anderen door via e-mail een koppeling of uitnodiging te verzenden. Uitgenodigde gebruikers kunnen hun identiteit verifiëren met de eenmalige wachtwoord code die wordt verzonden naar hun e-mail om toegang te krijgen tot de resources van de partner. [Meer informatie](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-januari 2021

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Zie [Wat is geautomatiseerd SaaS app User Provisioning in azure AD?](../app-provisioning/user-provisioning.md) voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-januari 2021

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In januari 2021 zijn de volgende 29 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue vorming](https://formation.bienvenue.pro/login), [Aida in de gezondheids zorg](https://aidaforparents.com/login/organizations), [internationale SOS Assistance-producten](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO dam](../saas-apps/ephoto-dam-tutorial.md) [, Insight,](../saas-apps/notion-tutorial.md) [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enter prise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [vacature filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), Synerise AI [-](../saas-apps/contentsquare-sso-tutorial.md) [groei ecosysteem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [illusive](../saas-apps/perimeter-81-tutorial.md) [Networks](../saas-apps/illusive-networks-tutorial.md), proexperience [, Splan](../saas-apps/proware-tutorial.md) [bezoekers,](../saas-apps/splan-visitor-tutorial.md) [Aruba Suite](../saas-apps/aruba-user-experience-insight-tutorial.md) [Enter prise Edition 81](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Open bare preview-Manager op tweede niveau kan worden ingesteld als alternatieve goed keurder

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Een extra optie wanneer u goed keurders selecteert, is nu beschikbaar in het recht beheer. Als u voor de eerste goed keurder ' Manager als fiatteur ' selecteert, beschikt u over een andere optie: ' tweede niveau Manager als alternatieve goed keurder ', beschikbaar om te kiezen in het veld alternatieve goed keurder. Als u deze optie selecteert, moet u een terugval-fiatteur toevoegen om de aanvraag door te sturen naar wanneer het systeem de tweede niveau Manager niet kan vinden. [Meer informatie](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Algemene Beschik baarheid: Navigeer rechtstreeks naar teams vanuit mijn Access-Portal

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
U kunt teams nu rechtstreeks vanuit de portal mijn toegang starten. 

Als u dit wilt doen, meldt u zich aan bij mijn toegang ( https://myaccess.microsoft.com/) , navigeert u naar ' toegangs pakketten ' en gaat u naar het tabblad actief om alle toegangs pakketten weer te geven waartoe u al toegang hebt. Wanneer u het geselecteerde toegangs pakket uitvouwt en aanwijst op teams, kunt u het openen door te klikken op de knop openen. [Meer informatie](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Verbeterde logboek registratie & End-User prompts voor Risk ante gast gebruikers

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

De logboek registratie-en End-User prompts voor Risk ante gast gebruikers zijn bijgewerkt. Meer informatie over [identiteits beveiliging en B2B-gebruikers](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>December 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Open bare preview-Azure AD B2C voor telefoon registratie en aanmelding met ingebouwd beleid

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Met behulp van het ingebouwde beleid kunnen IT-beheerders en ontwikkel aars van organisaties zich aanmelden en zich aanmelden met behulp van een telefoon nummer in gebruikers stromen. Lees de [telefoon registratie instellen en meld u aan voor gebruikers stromen (preview)](../../active-directory-b2c/phone-authentication-user-flows.md) voor meer informatie.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Algemene Beschik baarheid: de standaard instellingen voor beveiliging die nu standaard zijn ingeschakeld voor alle nieuwe tenants

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Als u gebruikers accounts wilt beveiligen, worden voor alle nieuwe tenants die zijn gemaakt op of na 12 november 2020, de standaard instellingen voor beveiliging ingeschakeld. Met de standaard instellingen voor beveiliging worden meerdere beleids regels afgedwongen, waaronder:
- Vereist dat alle gebruikers en beheerders zich registreren voor MFA met behulp van de app Microsoft Authenticator
- Vereist essentiële beheerders rollen voor het gebruik van MFA elke keer dat ze zich aanmelden. Alle andere gebruikers wordt indien nodig gevraagd om MFA. 
- Voor de verouderde verificatie wordt de Tenant breedte geblokkeerd. 

Lees [Wat zijn de standaard beveiligings instellingen?](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie.

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Algemene Beschik baarheid: ondersteuning voor groepen met Maxi maal 250.000e leden in AADConnect

**Type:** Gewijzigde functie  
**Service categorie:** AD Connect  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Micro soft heeft een nieuw eind punt (API) geïmplementeerd voor Azure AD Connect die de prestaties verbetert van de synchronisatie service-bewerkingen naar Azure Active Directory. Wanneer u het nieuwe [v2-eind punt](../hybrid/how-to-connect-sync-endpoint-api-v2.md)gebruikt, hebt u de mogelijkheid om op de hoogte te zijn van het exporteren en importeren naar Azure AD. Dit nieuwe eind punt ondersteunt de volgende scenario's:

- Groepen synchroniseren met Maxi maal 250.000 leden
- Prestatie verbeteringen bij het exporteren en importeren naar Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Algemeen Beschik baarheid-recht beheer dat beschikbaar is voor tenants in azure China Cloud

**Type:** Nieuwe functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 

De mogelijkheden van het beheer van rechten zijn nu beschikbaar voor alle tenants in de Azure China-Cloud. Ga naar onze [Identity governance-documentatie](https://docs.azure.cn/zh-cn/active-directory/governance/) site voor meer informatie.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-december 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Bizagi Studio voor digitale procesautomatisering](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-december 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In december 2020 zijn de volgende 18 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB One juridisch Hold](https://www.zylab.com/en/product/legal-hold), [gids](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [PIMs 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [Count me in-Operations dash board](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd personeels beheer](../saas-apps/rightcrowd-workforce-management-tutorial.md), [jll TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn talen oplossingen](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix-Federatie-app](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Rechtstreeks vanuit mijn Access-Portal naar teams navigeren

**Type:** Gewijzigde functie  
**Service categorie:** **Product mogelijkheid** voor beheer van gebruikers toegang: recht beheer

U kunt teams nu rechtstreeks vanuit mijn toegangs Portal starten. Hiertoe meldt u zich aan bij [Mijn toegang](https://myaccess.microsoft.com/), navigeert u naar **toegangs pakketten** en gaat u naar het tabblad **actief** om alle toegangs pakketten weer te geven waartoe u al toegang hebt. Wanneer u het toegangs pakket uitbreidt en aanwijst op teams, kunt u het openen door te klikken op de knop **openen** . 

Ga voor meer informatie over het gebruik van de portal van mijn toegang naar toegang [aanvragen voor een toegangs pakket in het rechts beheer van Azure AD](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Open bare preview-Manager op tweede niveau kan worden ingesteld als alternatieve goed keurder

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten

Een extra optie is nu beschikbaar in het goedkeurings proces in het recht beheer. Als u Manager als fiatteur selecteert voor de eerste goed keurder, hebt u nog een andere optie, tweede niveau Manager als alternatieve goed keurder, beschikbaar om te kiezen in het veld alternatieve goed keurder. Wanneer u deze optie selecteert, moet u een terugval-fiatteur toevoegen om de aanvraag door te sturen naar wanneer het systeem de tweede niveau Manager niet kan vinden.

Ga voor meer informatie naar [instellingen voor het wijzigen van de goed keuring voor een toegangs pakket in het beheer van rechten van Azure AD](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>November 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 en 3DES-afschaffing

**Type:** Plan voor wijziging  
**Service categorie:** Alle Azure AD-toepassingen  
**Product mogelijkheden:** Normalisatie

Azure Active Directory worden de volgende protocollen in Azure Active Directory wereld wijde regio's op 30 juni 2021.:

- TLS 1.0
- TLS 1.1
- 3DES-coderings suite (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Betrokken omgevingen zijn:
- Azure-commerciële Cloud
- Office 365 GCC en WW

Gerelateerde aankondiging alle combi Naties van client-server-en browser-server moet TLS 1,2-en moderne coderings suites gebruiken om een beveiligde verbinding te onderhouden met Azure Active Directory voor Azure, Office 365 en Microsoft 365 Services. Deze wijziging is gerelateerd aan [Azure Active Directory TLS 1,0 & 1,1 en de afschaffing van 3DES-code pakketten in US gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-november 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In november 2020 zijn de volgende 52 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Reizen & onkosten beheer](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning-bestands kiezer](https://pmteam.itslearning.com/), [crisis beheer](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum-Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [kijkend Analytics platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-Recruiting](https://recruit.talview.com/login), real time Translator, [Klaxoon](https://access.klaxoon.com/login), [podbean](../saas-apps/podbean-tutorial.md), [zCAL](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [netspark Enter prise](../saas-apps/netsparker-enterprise-tutorial.md), and [-Trak Tenant Experience platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [Eva check-in](https://my.evacheckin.com/organization), HowNow [webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [snijda risico beoordeling](../saas-apps/coupa-risk-assess-tutorial.md), [lucid (alle producten)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md) [,,](../saas-apps/perimeterx-tutorial.md) [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [natuur Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signing](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), Zons voorzien van [Software relaties CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [mentor® door eDriving: Business](https://www.edriving.com/), [Gradle Enter prise](https://gradle.com/)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Open bare preview-aangepaste rollen voor zakelijke apps

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
 [Aangepaste RBAC-rollen voor gedelegeerd beheer van bedrijfs toepassingen](../roles/custom-available-permissions.md) zijn nu beschikbaar als open bare preview. Deze nieuwe machtigingen zijn gebaseerd op de aangepaste rollen voor het beheer van app-registratie, waarmee nauw keurige controle kan worden uitgevoerd op de toegang die uw beheerders hebben. Na verloop van tijd worden extra machtigingen voor het delegeren van het beheer van Azure AD vrijgegeven.

Enkele algemene delegatie scenario's:
- toewijzing van gebruikers en groepen die toegang hebben tot op SAML gebaseerde toepassingen voor eenmalige aanmelding
- het maken van Azure AD Gallery-toepassingen
- elementaire SAML-configuraties bijwerken en lezen voor op SAML gebaseerde toepassingen voor eenmalige aanmelding
- beheer van handtekening certificaten voor op SAML gebaseerde toepassingen voor eenmalige aanmelding
- Update van verlopen aanmeldings certificaten e-mail adressen voor op SAML gebaseerde toepassingen voor eenmalige aanmelding
- Update van de hand tekening van het SAML-token en aanmeldings algoritme voor op SAML gebaseerde toepassingen voor eenmalige aanmelding
- gebruikers kenmerken en claims voor op SAML gebaseerde toepassingen voor eenmalige aanmelding maken, verwijderen en bijwerken
- mogelijkheid tot het inschakelen, uitschakelen en opnieuw starten van inrichtings taken
- updates voor kenmerk toewijzing
- de mogelijkheid om inrichtings instellingen te lezen die zijn gekoppeld aan het object
- de mogelijkheid om inrichtings instellingen te lezen die zijn gekoppeld aan uw Service-Principal
- de mogelijkheid om toegang tot toepassingen te verlenen voor inrichting

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Open bare preview-AD-toepassingsproxy Azure biedt systeem eigen ondersteuning voor eenmalige aanmelding voor toepassingen die gebruikmaken van headers voor verificatie

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control
 
Azure Active Directory-toepassings proxy (Azure AD) biedt systeem eigen ondersteuning voor eenmalige aanmelding voor toepassingen die gebruikmaken van headers voor authenticatie. U kunt in azure AD vereiste header-waarden configureren voor uw toepassing. De header waarden worden via toepassings proxy verzonden naar de toepassing. Zie voor meer informatie de op [headers gebaseerde eenmalige aanmelding voor on-premises apps met Azure AD-App proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Algemene Beschik baarheid-Azure AD B2C telefonische aanmelding en aanmelding met aangepast beleid

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

Met het registreren van telefoon nummers en aanmelding kunnen ontwikkel aars en ondernemingen hun klanten toestaan zich aan te melden en zich aan te melden met een eenmalig wacht woord dat via SMS naar het telefoon nummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook hun telefoon nummer wijzigen als de toegang tot hun telefoon verloren is gegaan. Met de kracht van aangepast beleid kunnen ontwikkel aars en ondernemingen hun merk via de pagina aanpassen. Meer informatie over het [instellen van aanmelding via de telefoon en het aanmelden met aangepaste beleids regels in azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie: november 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Boter-kaas en mobiel](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Zie [Gebruikers inrichten voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md)voor meer informatie.
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Open bare preview-e-mail Sign-In met ProxyAddresses die nu kan worden geïmplementeerd via gefaseerde implementatie

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Tenant beheerders kunnen nu gefaseerde implementatie gebruiken om e-mail Sign-In te implementeren met ProxyAddresses naar specifieke Azure AD-groepen. Dit kan helpen bij het uitproberen van de functie voordat u deze implementeert in de volledige Tenant via het beleid voor het detecteren van de thuis domein. Instructies voor het implementeren van e-mail Sign-In met ProxyAddresses via gefaseerde implementatie vindt u in de [documentatie](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Beperkte preview-aanmelding Diagnostic

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Met de eerste preview-versie van de diagnostische aanmelding kunnen beheerders nu de gebruikers aanmeldingen bekijken. Beheerders kunnen contextuele, specifieke en relevante details ontvangen en richt lijnen voor wat er is gebeurd tijdens het aanmelden en het oplossen van problemen. De diagnose is beschikbaar op het niveau van Azure AD en voor het opsporen en oplossen van problemen met voorwaardelijke toegang. De diagnostische scenario's die in deze release worden behandeld, zijn voorwaardelijke toegang, Multi-Factor Authentication en geslaagde aanmelding.

Zie [Wat is aanmeldings diagnose in azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md)voor meer informatie.
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Verbeterde onbekende aanmeld eigenschappen

**Type:** Gewijzigde functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

  Onbekende detecties van aanmeldings eigenschappen zijn bijgewerkt. Klanten kunnen meer detecties van niet-vertrouwde aanmeldings eigenschappen met een hoog risico op de hoogte stellen. Zie [Wat is risico?](../identity-protection/concept-identity-protection-risks.md) voor meer informatie.
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Open bare preview-versie van de Cloud-inrichtings agent is nu beschikbaar (version: 1.1.281.0)

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD-Cloud inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De inrichtings agent voor Cloud is vrijgegeven in de open bare preview-versie en is nu beschikbaar via de portal. Deze release bevat verschillende verbeteringen, waaronder, ondersteuning voor GMSA voor uw domeinen, waarmee betere beveiliging, verbeterde initiële synchronisatie cycli en ondersteuning voor grote groepen worden geboden. Bekijk de [geschiedenis](../app-provisioning/provisioning-agent-release-version-history.md) van de release versie voor meer informatie. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>API-eind punt voor BitLocker-herstel sleutel nu onder/informationProtection

**Type:** Gewijzigde functie  
**Service categorie:** Toegangs beheer voor apparaten  
**Product mogelijkheden:** Levenscyclus beheer van apparaten
 
Voorheen kon u BitLocker-sleutels herstellen via het/BitLocker-eind punt. We zullen dit eind punt uiteindelijk afnemen en klanten moeten beginnen met het gebruiken van de API die nu onder/informationProtection. 

Raadpleeg de [BitLocker Recovery API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) voor updates in de documentatie om deze wijzigingen weer te geven.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Algemene Beschik baarheid van de ondersteuning van toepassings proxy voor de Extern bureaublad-services HTML5-webclient

**Type:** Gewijzigde functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control
 
De web-client voor Azure AD-toepassingsproxy-ondersteuning voor Extern bureaublad-services (RDS) is nu algemeen beschikbaar. Met de RDS-webclient kunnen gebruikers toegang krijgen tot Extern bureaublad-infra structuur via elke HTLM5 browser zoals micro soft Edge, Internet Explorer 11, Google Chrome, enzovoort. Gebruikers kunnen met externe apps of Bureau bladen werken, zoals bij een lokaal apparaat. 

Door Azure AD-toepassingsproxy te gebruiken, kunt u de beveiliging van uw RDS-implementatie verhogen door pre-authenticatie en beleid voor voorwaardelijke toegang af te dwingen voor alle typen uitgebreide client-apps. Zie [extern bureaublad publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) voor meer informatie.
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nieuwe verbeterde dynamische groeps service is in open bare preview

**Type:** Gewijzigde functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking
 
Verbeterde dynamische groeps service is nu beschikbaar als open bare preview. Nieuwe klanten die dynamische groepen in hun tenants maken, gebruiken de nieuwe service. De tijd die nodig is om een dynamische groep te maken, is evenredig met de grootte van de groep die wordt gemaakt in plaats van de grootte van de Tenant. Met deze update worden de prestaties van grote tenants aanzienlijk verbeterd wanneer klanten kleinere groepen maken. 

De nieuwe service is ook gericht op het volt ooien van leden toevoegen en verwijderen vanwege wijzigingen in een paar minuten. Ook wordt de verwerking van tenants niet geblokkeerd door afzonderlijke verwerkings fouten. Zie onze [documentatie](../enterprise-users/groups-create-rule.md)voor meer informatie over het maken van dynamische groepen.
 
---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD on-premises hybride agents die invloed hebben op wijzigingen in azure TLS-certificaten

**Type:** Plan voor wijziging  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Onafhankelijk

Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze update wordt veroorzaakt door de huidige CA-certificaten die niet voldoen aan een van de vereisten voor de basis lijn van de CA/browser-forum. Deze wijziging is van invloed op Azure AD Hybrid-agents die zijn geïnstalleerd op locatie en die beveiligde omgevingen hebben met een vaste lijst basis certificaten en moeten worden bijgewerkt om de nieuwe certificaat verleners te vertrouwen.

Deze wijziging leidt ertoe dat de service wordt onderbroken als u niet onmiddellijk actie onderneemt. Deze agents bevatten [toepassings proxy connectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) voor externe toegang tot on-premises, [Passthrough-verificatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenten waarmee uw gebruikers zich kunnen aanmelden bij toepassingen met dezelfde wacht woorden, en preview-agents [voor Cloud inrichting](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) die AD naar Azure AD Sync uitvoeren. 

Als u een omgeving met firewall regels hebt ingesteld zodat alleen uitgaande oproepen naar een specifieke CRL (certificaatintrekkingslijst) kunnen worden gedownload, moet u de volgende CRL-en OCSP-Url's toestaan. Zie  [wijzigingen in azure TLS-certificaten](../../security/fundamentals/tls-certificate-changes.md)voor meer informatie over de wijziging en de CRL-en OCSP-url's waarmee toegang kan worden ingeschakeld.

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Inrichtings gebeurtenissen worden uit audit logboeken verwijderd en alleen gepubliceerd voor het inrichtings logboek

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Activiteiten door de SCIM- [inrichtings service](../app-provisioning/user-provisioning.md) worden vastgelegd in de audit logboeken en de inrichtings Logboeken. Dit omvat activiteiten zoals het maken van een gebruiker in ServiceNow, groeperen in GSuite of het importeren van een rol uit AWS. In de toekomst worden deze gebeurtenissen alleen gepubliceerd in de inrichtings Logboeken. Deze wijziging wordt geïmplementeerd om te voor komen dat er dubbele gebeurtenissen in Logboeken worden gemaakt en er worden extra kosten in rekening gebracht door klanten die de logboeken in log Analytics gebruiken. 

We bieden een update wanneer een datum is voltooid. Deze afschaffing is niet gepland voor het kalender jaar 2020. 

> [!NOTE]
> Dit heeft geen invloed op gebeurtenissen in de audit logboeken buiten de synchronisatie gebeurtenissen die door de inrichtings service worden gegenereerd. Gebeurtenissen, zoals het maken van een toepassing, beleid voor voorwaardelijke toegang, een gebruiker in de map, enzovoort, worden in de audit Logboeken opgenomen. [Meer informatie](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD on-premises hybride agents die worden beïnvloed door wijzigingen in het certificaat van Azure Transport Layer Security (TLS)

**Type:** Plan voor wijziging  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Onafhankelijk
 
Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Er wordt een update uitgevoerd vanwege de huidige CA-certificaten die niet voldoen aan de basis vereisten voor de CA/browser-forum. Deze wijziging is van invloed op Azure AD Hybrid-agents die zijn geïnstalleerd op locatie en die beveiligde omgevingen hebben met een vaste lijst basis certificaten. Deze agents moeten worden bijgewerkt om de nieuwe certificaat verleners te vertrouwen.

Deze wijziging leidt ertoe dat de service wordt onderbroken als u niet onmiddellijk actie onderneemt. Deze agents zijn onder andere: 
- [Application proxy-connectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) voor externe toegang tot on-premises 
- [Passthrough-verificatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenten waarmee uw gebruikers zich kunnen aanmelden bij toepassingen met dezelfde wacht woorden
- [Cloud inrichting preview](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) -agents die AD naar Azure AD Sync uitvoeren. 

Als u een omgeving met firewall regels hebt ingesteld zodat alleen uitgaande oproepen naar een specifieke CRL (certificaatintrekkingslijst) kunnen worden gedownload, moet u CRL-en OCSP-Url's toestaan. Zie  [wijzigingen in azure TLS-certificaten](../../security/fundamentals/tls-certificate-changes.md)voor meer informatie over de wijziging en de CRL-en OCSP-url's waarmee toegang kan worden ingeschakeld.
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0-, TLS 1,1-en 3DES-afschaffing in US Gov Cloud

**Type:** Plan voor wijziging  
**Service categorie:** Alle Azure AD-toepassingen  
**Product mogelijkheden:** Normalisatie
 
Azure Active Directory worden de volgende protocollen op 31 maart 2021 afschaft:
- TLS 1.0
- TLS 1.1
- 3DES-coderings suite (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Alle combi Naties van client-server-en browser-server moet TLS 1,2-en moderne coderings suites gebruiken om een beveiligde verbinding te onderhouden met Azure Active Directory voor Azure, Office 365 en Microsoft 365 Services.

Betrokken omgevingen zijn:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Toepassingen toewijzen aan rollen op AU en object bereik

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Deze functie biedt de mogelijkheid om een toepassing (SPN) toe te wijzen aan een beheerdersrol op het bereik van de beheer eenheid. Zie [scoped rollen toewijzen aan een beheer eenheid](../roles/admin-units-assign-roles.md)voor meer informatie.

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>U kunt gast gebruikers nu uitschakelen en verwijderen wanneer ze geen toegang meer hebben tot een bron

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Identity governance
 
Uitschakelen en verwijderen is een geavanceerd besturings element in azure AD-toegangs beoordelingen waarmee organisaties beter externe gasten in groepen en apps kunnen beheren. Als gasten worden geweigerd bij een toegangs beoordeling, worden ze door **uitschakelen en verwijderen** automatisch geblokkeerd voor het aanmelden van 30 dagen. Na 30 dagen worden ze volledig uit de Tenant verwijderd.

Zie voor meer informatie over deze functie [externe identiteiten uitschakelen en verwijderen met Azure AD-toegangs beoordelingen](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Toegangs beoordeling van makers kan aangepaste berichten toevoegen aan revisoren

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Identity governance
 
In azure AD-toegangs beoordelingen kunnen beheerders die recensies maken, nu een aangepast bericht naar de revisors schrijven. Revisoren zien het bericht in de e-mail die ze ontvangen en vragen om de beoordeling te volt ooien. Zie stap 14 van de sectie [een of meer toegangs beoordelingen maken](../governance/create-access-review.md#create-one-or-more-access-reviews) voor meer informatie over het gebruik van deze functie.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-oktober 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integratie-assistent voor Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
De ervaring voor de integratie-assistent (preview) is nu beschikbaar voor Azure AD B2C App-registraties. Deze ervaring helpt u bij het configureren van uw toepassing voor algemene scenario's. Meer informatie over [Best practices en aanbevelingen van micro soft Identity platform](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>ID van de rol sjabloon weer geven in Azure Portal gebruikers interface

**Type:** Nieuwe functie  
**Service categorie:** Azure-rollen  
**Product mogelijkheden:** Access Control
 

U kunt nu de sjabloon-ID van elke Azure AD-rol weer geven in de Azure Portal. Selecteer in azure AD de  **Beschrijving** van de geselecteerde rol. 

Het is raadzaam om in plaats van de weergave naam rollen sjabloon-Id's te gebruiken in hun Power shell-script en code. De sjabloon-ID van de functie wordt ondersteund voor gebruik in [directoryRoles](/graph/api/resources/directoryrole) -en [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) -objecten. Zie [ingebouwde rollen van Azure AD](../roles/permissions-reference.md)voor meer informatie over de id van de functie sjabloon.

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>API-connectors voor Azure AD B2C registratie van gebruikers stromen is nu beschikbaar als open bare preview

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 

API-connectors zijn nu beschikbaar voor gebruik met Azure Active Directory B2C. Met API-connectors kunt u Web-Api's gebruiken om uw registratie van gebruikers te wijzigen en te integreren met externe Cloud systemen. U kunt API-connectors gebruiken voor het volgende:

- Integreren met aangepaste goedkeurings werk stromen
- Gebruikers invoer gegevens valideren
- Gebruikers kenmerken overschrijven 
- Aangepaste bedrijfs logica uitvoeren 

 Ga naar de [API-connectors gebruiken voor het aanpassen en uitbreiden van de aanmeldings](../../active-directory-b2c/api-connectors-overview.md) documentatie voor meer informatie.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Eigenschap State voor verbonden organisaties in het recht beheer

**Type:** Nieuwe functie  
**Service categorie:** Mogelijkheden van Directory Management- **product:** recht beheer
 

 Alle verbonden organisaties hebben nu een extra eigenschap met de naam ' state '. Met de status wordt bepaald hoe de verbonden organisatie wordt gebruikt in beleids regels die verwijzen naar ' alle geconfigureerde verbonden organisaties '. De waarde is ' geconfigureerd ' (wat betekent dat de organisatie zich in het bereik van beleids regels bevindt dat de component ' all ' gebruikt) of ' voorgesteld ' (wat betekent dat de organisatie niet binnen het bereik valt).  

Hand matig gemaakte verbonden organisaties hebben de standaard instelling ' geconfigureerd '. Ondertussen worden automatisch gemaakte records (gemaakt via beleids regels waarmee gebruikers van Internet toegang kunnen krijgen) standaard ingesteld op voorgesteld.  Alle verbonden organisaties die vóór september 9 2020 zijn gemaakt, worden ingesteld op geconfigureerd. Beheerders kunnen deze eigenschap indien nodig bijwerken. [Meer informatie](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory externe identiteiten hebben nu Premium geavanceerde beveiligings instellingen voor B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Op Risico's gebaseerde voorwaardelijke toegang en risico detectie functies van identiteits beveiliging zijn nu beschikbaar in [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Met deze geavanceerde beveiligings functies kunnen klanten nu het volgende doen:
- Maak gebruik van intelligente inzichten om Risico's te beoordelen met B2C-apps en eind gebruikers accounts. Detecties zijn onder andere ongewoone reizen, anonieme IP-adressen, aan schadelijke software gekoppelde IP-adressen en Azure AD Threat Intelligence. Er zijn ook Portal-en op API gebaseerde rapporten beschikbaar.
- Verhelp automatisch Risico's door beleid voor adaptief verificatie te configureren voor B2C-gebruikers. App-ontwikkel aars en beheerders kunnen real-time risico beperken door multi-factor Authentication (MFA) te vereisen of de toegang te blok keren, afhankelijk van het niveau van de gebruikers risico dat is gedetecteerd, met extra besturings elementen die beschikbaar zijn op basis van locatie, groep en app.
- Integreer met Azure AD B2C gebruikers stromen en aangepaste beleids regels. Voor waarden kunnen worden geactiveerd vanuit ingebouwde gebruikers stromen in Azure AD B2C of kunnen worden opgenomen in aangepaste beleids regels voor B2C. Net als bij andere aspecten van de B2C-gebruikers stroom kunnen de berichten voor de ervaring van de eind gebruiker worden aangepast. Aanpassing is gebaseerd op de spraak, het merk en de mogelijkheden van de organisatie.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-oktober 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In oktober 2020 zijn de volgende 27 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity supertoe](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys cloud Integration voor Azure](https://apps.mypurecloud.com/msteams-integration/), [zone technologieën Portal](https://portail.zonetechnologie.com/signin), [beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CHECKPROOF](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [AtSpoke](http://atspoke.com/login), [afspraak herinnering](https://app.appointmentreminder.co.nz/account/login), [Cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [greetend](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web vracht-Air](../saas-apps/web-cargo-air-tutorial.md), [loop stroom CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), Samsara [, Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) [](../saas-apps/samsara-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Inrichtings logboeken kunnen nu worden gestreamd naar log Analytics

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 

Publiceer uw inrichtings logboeken naar log Analytics om het volgende te doen:
- Inrichtings logboeken voor meer dan 30 dagen opslaan
- Aangepaste waarschuwingen en meldingen definiëren
- Dash boards bouwen om de logboeken te visualiseren
- Complexe query's uitvoeren om de logboeken te analyseren 

Zie [begrijpen hoe inrichting wordt geïntegreerd met Azure monitor-logboeken](../app-provisioning/application-provisioning-log-analytics.md)voor meer informatie over het gebruik van de functie.
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Inrichtings logboeken kunnen nu worden weer gegeven door eigen aren van toepassingen

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
U kunt nu eigen aren van toepassingen toestaan om activiteiten te bewaken door de inrichtings service en problemen op te lossen zonder een bevoegde rol te bieden of een knel punt te maken. [Meer informatie](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>De naam van 10 Azure Active Directory-rollen wijzigen

**Type:** Gewijzigde functie  
**Service categorie:** Azure-rollen  
**Product mogelijkheden:** Access Control
 
Sommige ingebouwde rollen van Azure Active Directory (AD) hebben namen die verschillen van de functies die worden weer gegeven in Microsoft 365 beheer centrum, de Azure AD-Portal en Microsoft Graph. Deze inconsistentie kan problemen veroorzaken in geautomatiseerde processen. Met deze update worden de namen van 10 rolnaam gewijzigd zodat ze consistent zijn. De volgende tabel bevat de nieuwe rolnaams:

![Tabel met nieuwe rolnaams](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C ondersteuning voor auth code flow voor SPAs met behulp van MSAL JS 2. x

**Type:** Gewijzigde functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
MSAL.js versie 2. x bevat nu ondersteuning voor de autorisatie code stroom voor web-apps met één pagina (SPAs). Azure AD B2C biedt nu ondersteuning voor het gebruik van het type SPA-app op de Azure Portal en het gebruik van MSAL.js autorisatie code flow met PKCE voor apps met één pagina. Zo kunt u met behulp van Azure AD B2C de eenmalige aanmelding met nieuwere browsers onderhouden en de aanbevelingen van nieuwere verificatie protocollen. Ga aan de slag met het [registreren van een single-page-toepassing (Spa) in azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) zelf studie.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Updates voor het onthouden van Multi-Factor Authentication (MFA) voor een instelling voor een vertrouwd apparaat

**Type:** Gewijzigde functie  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

We hebben onlangs de [onthouden multi-factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) voor een functie van vertrouwd apparaat bijgewerkt om de verificatie voor maxi maal 365 dagen uit te breiden. Azure Active Directory (Azure AD) Premium-licenties kunnen ook gebruikmaken van het [beleid voor voorwaardelijke toegang – aanmeldings frequentie](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) dat meer flexibiliteit biedt voor het opnieuw verifiëren van instellingen.

Voor een optimale gebruikers ervaring kunt u het beste de aanmeldings frequentie voor voorwaardelijke toegang gebruiken om de levens duur van sessies op vertrouwde apparaten, locaties of sessies met een laag risico uit te breiden als alternatief voor de instelling MFA onthouden voor een vertrouwde apparaat. Bekijk onze [meest recente richt lijnen voor het optimaliseren van de herauthenticatie-ervaring](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)om aan de slag te gaan.

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-september 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot-beveiligings bewustmaking](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>De open bare preview-versie van Cloud inrichting vernieuwen

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Cloud voor het inrichten van **producten:** Identity Lifecycle Management
 
Azure AD Connect Cloud Provisioning open bare preview-functies vernieuwt twee belang rijke verbeteringen die zijn ontwikkeld door feedback van klanten: 

- Ervaring met kenmerk toewijzing via Azure Portal

    Met deze functie kunnen IT-beheerders gebruikers-, groeps-of contact kenmerken van AD toewijzen aan Azure AD met behulp van de verschillende toewijzings typen die vandaag beschikbaar zijn. Kenmerk toewijzing is een functie die wordt gebruikt voor het standaardiseren van de waarden van de kenmerken van Active Directory naar Azure Active Directory. Eén kan bepalen of de kenmerk waarde direct moet worden toegewezen aan de naam van AD naar Azure AD of dat expressies moeten worden gebruikt om de kenmerk waarden bij het inrichten van gebruikers te transformeren. [Meer informatie](../cloud-sync/how-to-attribute-mapping.md)

- Inrichting op aanvraag of gebruikers ervaring testen

    Wanneer u uw configuratie hebt ingesteld, wilt u wellicht testen of de gebruikers transformatie werkt zoals verwacht voordat u deze toepast op alle gebruikers binnen het bereik. Met inrichting op aanvraag kunnen IT-beheerders de DN (Distinguished Name) van een AD-gebruiker invoeren en zien of ze worden gesynchroniseerd zoals verwacht. Inrichting op aanvraag biedt een uitstekende manier om ervoor te zorgen dat de kenmerk toewijzingen die u eerder hebt uitgevoerd zoals verwacht. [Meer informatie](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Gecontroleerd BitLocker-herstel in azure AD-open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beheer voor apparaten  
**Product mogelijkheden:** Levenscyclus beheer van apparaten
 
Wanneer IT-beheerders of eind gebruikers BitLocker-herstel sleutel (s) hebben gelezen waartoe ze toegang hebben, wordt door Azure Active Directory nu een audit logboek gegenereerd waarin wordt vastgelegd wie de herstel sleutel heeft geopend. Dezelfde controle bevat details van het apparaat waaraan de BitLocker-sleutel is gekoppeld.

Eind gebruikers [hebben via mijn account toegang tot hun herstel sleutels](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). IT-beheerders hebben toegang tot herstel sleutels via de [API van de BitLocker-herstel sleutel in bèta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) of via de Azure AD-Portal. Zie [BitLocker-sleutels weer geven of kopiëren in de Azure AD-Portal](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)voor meer informatie.

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams ingebouwde rol van de beheerder van de apparaten

**Type:** Nieuwe functie  
**Service categorie:** RBAC  
**Product mogelijkheden:** Access Control
 
Gebruikers met de rol [teams van apparaten](../roles/permissions-reference.md#teams-devices-administrator) kunnen [teams, gecertificeerde apparaten](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) beheren vanuit het beheer centrum teams. 

Met deze rol kan de gebruiker alle apparaten in één oogopslag weer geven, met de mogelijkheid om apparaten te zoeken en te filteren. De gebruiker kan ook de details van elk apparaat controleren, inclusief het aangemelde account en het merk en model van het apparaat. De gebruiker kan de instellingen op het apparaat wijzigen en de software versies bijwerken. Deze rol verleent geen machtigingen om de activiteit teams te controleren en de kwaliteit van het apparaat aan te roepen.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Geavanceerde query mogelijkheden voor Directory-objecten

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Alle nieuwe query mogelijkheden die zijn geïntroduceerd voor Directory objecten in azure AD Api's zijn nu beschikbaar in het eind punt v 1.0 en productie gereed. Ontwikkel aars kunnen Directory-objecten en gerelateerde koppelingen tellen, zoeken, filteren en sorteren met behulp van de standaard OData-Opera tors.

Raadpleeg de documentatie [hier](https://aka.ms/BlogPostMezzoGA)voor meer informatie. u kunt ook feedback verzenden met deze [korte enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Open bare Preview: voortdurende toegangs beoordeling voor tenants die beleids regels voor voorwaardelijke toegang hebben geconfigureerd

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De evaluatie van voortdurende toegang (CAE) is nu beschikbaar in de open bare Preview voor Azure AD-tenants met beleid voor voorwaardelijke toegang. Met CAE worden essentiële beveiligings gebeurtenissen en-beleids regels in realtime geëvalueerd. Dit omvat het uitschakelen van accounts, het opnieuw instellen van wacht woorden en het wijzigen van de locatie. Zie [evaluatie van continue toegang](../conditional-access/concept-continuous-access-evaluation.md)voor meer informatie.

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Open bare Preview: vraag gebruikers om een toegangs pakket aanvullende vragen om goedkeurings beslissingen te verbeteren

**Type:** Nieuwe functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Beheerders kunnen er nu voor zorgen dat gebruikers die een toegangs pakket aanvragen, meer vragen beantwoorden dan alleen de zakelijke reden in de mijn Access-portal van Azure AD-rechten beheer. De antwoorden van de gebruikers worden vervolgens weer gegeven aan de goed keurders, zodat ze een nauw keurigere goed keuring voor toegang kunnen krijgen. Zie [aanvullende gegevens van de aanvrager voor goed keuring verzamelen (preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)voor meer informatie.
 
---

### <a name="public-preview-enhanced-user-management"></a>Open bare Preview: verbeterd gebruikers beheer

**Type:** Nieuwe functie  
**Service categorie:** Gebruikers beheer  
**Product mogelijkheden:** Gebruikers beheer
 

De Azure AD-Portal is bijgewerkt, zodat u gebruikers gemakkelijker kunt vinden op de pagina's alle gebruikers en verwijderde gebruikers. Wijzigingen in de preview zijn onder andere: 
- Meer zicht bare gebruikers eigenschappen, waaronder de object-ID, de synchronisatie status van de Directory, het aanmaak type en de identiteits verlener.
- Met zoeken kunt u nu gecombineerde Zoek opdrachten van namen, e-mails en object-Id's.
- Verbeterd filteren op gebruikers type (lid, gast en geen), adreslijst synchronisatie status, aanmaak type, bedrijfs naam en domein naam.
- Nieuwe sorteer mogelijkheden voor eigenschappen zoals naam, user principal name en verwijderings datum.
- Een nieuw totaal aantal gebruikers dat wordt bijgewerkt met Zoek opdrachten of filters.

Zie [verbeteringen voor gebruikers beheer (preview) in azure Active Directory](../enterprise-users/users-search-enhanced.md)voor meer informatie.

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nieuw notitie veld voor zakelijke toepassingen

**Type:** Nieuwe functie  
**Service categorie:** **Product mogelijkheden** van ENTER prise apps: SSO

U kunt gratis tekst notities toevoegen aan zakelijke toepassingen. U kunt alle relevante informatie toevoegen die u helpt bij het beheer van toepassingen onder bedrijfs toepassingen. Zie [Quick Start: eigenschappen configureren voor een toepassing in uw Azure Active Directory-Tenant (Azure AD)](../manage-apps/add-application-portal-configure.md)voor meer informatie. 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-september 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In september 2020 zijn de volgende 34 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[VMware horizon-Unified Access Gateway](), [Pulse Secure pc's](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [gender geschiktheid](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [grammaticaal](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [Raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [mobiele kluis](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [soonlijk Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [beveiligde ondertekening Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [beveiligde ondertekening Enterprise Portal Aad-installatie](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle people-protected by F5 BIG-IP apm](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden: https://aka.ms/AppsTutorial .

Lees de volgende informatie voor het weer geven van uw toepassing in de app-galerie van Azure AD: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nieuwe delegering functie in azure AD-rechts beheer: Access package Assignment Manager

**Type:** Nieuwe functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 
Er is een nieuwe rol voor toegangs pakket toewijzings beheer toegevoegd aan Azure AD-rechts beheer om gedetailleerde machtigingen te bieden voor het beheren van toewijzingen. U kunt nu taken overdragen aan een gebruiker met deze rol, die het beheer van toewijzingen van een toegangs pakket kan delegeren aan een zakelijke eigenaar. Een toegangs pakket toewijzings beheer kan echter het toegangs pakket beleid of andere eigenschappen die door de beheerders zijn ingesteld, niet wijzigen. 

Met deze nieuwe rol profiteert u van de mini maal benodigde bevoegdheden voor het delegeren van beheer van toewijzingen en het onderhouden van beheer beheer voor alle andere configuraties van het toegangs pakket. Zie [rechten beheer rollen](../governance/entitlement-management-delegate.md#entitlement-management-roles)voor meer informatie.
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Wijzigingen in de onboarding-stroom van Privileged Identity Management

**Type:** Gewijzigde functie  
**Service categorie:** Privileged Identity Management  
**Product mogelijkheden:** Privileged Identity Management
 
Voorheen heeft het onboarding van Privileged Identity Management (PIM) vereiste gebruikers toestemming en een onboarding-stroom in de Blade PIM opgenomen die de inschrijving in azure AD MFA bijwerkt. Met de recente integratie van PIM-ervaring op de Blade rollen en beheerders van Azure AD, wordt deze ervaring verwijderd. Elke Tenant met een geldige P2-licentie wordt automatisch onboarded naar PIM.

Onboarding naar PIM heeft geen direct schadelijk effect op uw Tenant. U kunt de volgende wijzigingen verwachten:
- Aanvullende toewijzings opties zoals actief versus. in aanmerking komen als begin-en eind tijd wanneer u een toewijzing maakt in de Blade PIM of Azure AD-rollen en Administrators. 
- Extra bereik mechanismen, zoals administratieve eenheden en aangepaste rollen, worden direct in de toewijzings ervaring geïntroduceerd. 
- Als u een globale beheerder of beheerder van een bevoorrechte rol bent, kunt u beginnen met het ophalen van enkele extra e-mail berichten zoals het wekelijks samen vatting van PIM. 
- U ziet mogelijk ook MS-PIM-Service-Principal in het controle logboek dat betrekking heeft op de roltoewijzing. Deze verwachte wijziging is niet van invloed op uw normale werk stroom.

 Zie [beginnen met privileged Identity Management](../privileged-identity-management/pim-getting-started.md)voor meer informatie.

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Beheer van rechten van Azure AD: in het deel venster met toegangs pakket resources worden nu standaard de resources weer gegeven die momenteel in de geselecteerde catalogus staan

**Type:** Gewijzigde functie  
**Service categorie:** Beheer van gebruikers toegang  
**Product mogelijkheden:** Beheer rechten
 

In de stroom voor het maken van het toegangs pakket, onder het tabblad Resource rollen, wordt het gedrag van het deel venster selecteren gewijzigd. Op dit moment is het standaard gedrag om alle resources weer te geven die eigendom zijn van de gebruiker en resources die zijn toegevoegd aan de geselecteerde catalogus. 

Deze ervaring wordt zodanig gewijzigd dat er standaard alleen de resources worden weer gegeven die momenteel aan de catalogus zijn toegevoegd, zodat gebruikers eenvoudig resources uit de catalogus kunnen kiezen. De update helpt u bij het detecteren van de resources om deze toe te voegen aan toegangs pakketten, en vermindert het risico van het per ongeluk toevoegen van resources die eigendom zijn van de gebruiker die geen deel uitmaakt van de catalogus. Zie [een nieuw toegangs pakket maken in azure AD-rechts beheer](../governance/entitlement-management-access-package-create.md#resource-roles)voor meer informatie.
 
---
