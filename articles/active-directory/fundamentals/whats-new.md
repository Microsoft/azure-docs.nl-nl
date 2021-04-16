---
title: Wat is nieuw? Opmerkingen bij de release - Azure Active Directory | Microsoft Docs
description: Meer informatie over wat er nieuw is Azure Active Directory; zoals de meest recente opmerkingen bij de release, bekende problemen, opgeloste fouten, afgeschafte functionaliteit en toekomstige wijzigingen.
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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532306"
---
# <a name="whats-new-in-azure-active-directory"></a>Wat is er nieuw in Azure Active Directory?

>U kunt een melding krijgen wanneer deze pagina is bijgewerkt door de URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` te kopiëren en plakken in uw ![pictogram van RSS-feedlezer](./media/whats-new/feed-icon-16x16.png) feedlezer.

Azure AD ontvangt voortdurend verbeteringen. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

Deze pagina wordt maandelijks bijgewerkt, dus keer deze regelmatig opnieuw. Als u items zoekt die ouder zijn dan zes maanden, kunt u deze vinden in Archief voor Wat is er nieuw [in Azure Active Directory.](whats-new-archive.md)

---

## <a name="march-2021"></a>Maart 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Richtlijnen voor het inschakelen van ondersteuning voor TLS 1.2 in uw omgeving, ter voorbereiding op aanstaande afschaffing van Azure AD TLS 1.0/1.1

**Type:** Plannen voor wijziging  
**Servicecategorie:** N/a  
**Productmogelijkheid:** Normen

Azure Active Directory worden vanaf 30 juni 2021 de volgende protocollen in Azure Active Directory wereldwijde regio's afgeschaft:


- TLS 1.0
- TLS 1.1
- 3DES-coderingssuite (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Betrokken omgevingen zijn onder andere:

- Azure Commercial Cloud
- Office 365 GCC en WW

Raadpleeg Enable support [for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (Ondersteuning voor TLS 1.2 in uw omgeving inschakelen voor afschaffing van Azure AD TLS 1.1 en 1.0) voor aanvullende richtlijnen.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Openbare preview- Azure AD-rechtenbeheer ondersteunt nu SharePoint Online met meerdere geografische regio's

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Rechtenbeheer
 
Voor organisaties die SharePoint Online met meerdere geografische gebieden gebruiken, kunt u nu sites uit specifieke omgevingen met meerdere geografische gebieden opnemen in uw rechtenbeheertoegangspakketten. [Meer informatie](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Openbare preview- Verwijderde apps herstellen vanuit App-registraties

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Ontwikkelaarservaring
 
Klanten kunnen nu verwijderde app-registraties bekijken, herstellen en permanent verwijderen uit de Azure Portal. Dit geldt alleen voor toepassingen die zijn gekoppeld aan een directory, niet voor toepassingen van een Microsoft-account. [Meer informatie](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Openbare preview- Nieuwe gebruikersactie in Voorwaardelijke toegang voor het registreren of samenvoegen van apparaten

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection
 
 Er is een nieuwe gebruikersactie met de naam 'Apparaten registreren of lid worden' beschikbaar in Voorwaardelijke toegang. Met deze gebruikersactie kunt u MFA-beleidsregels (Multi-Factor Authentication) voor Azure AD-apparaatregistratie bepalen. 

Op dit moment kunt u met deze gebruikersactie MFA alleen inschakelen als besturingselement wanneer gebruikers apparaten registreren of lid worden van Azure AD. Andere besturingselementen die afhankelijk zijn van of niet van toepassing zijn op Azure AD-apparaatregistratie, worden uitgeschakeld met deze gebruikersactie. [Meer informatie](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Openbare preview- - Connectorgroepen optimaliseren voor het gebruik van de dichtstbijzijnde toepassingsproxy cloudservice

**Type:** Nieuwe functie  
**Servicecategorie:** App-proxy  
**Productmogelijkheid:** Access Control
 
Met deze nieuwe mogelijkheid kunnen connectorgroepen worden toegewezen aan de dichtstbijzijnde regionale toepassingsproxy service waarin een toepassing wordt gehost. Dit kan de prestaties van apps verbeteren in scenario's waarin apps worden gehost in andere regio's dan de regio van de thuis-tenant. [Meer informatie](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Openbare preview- External Identities Self-Service registreren in AAD met behulp van e-mailaccounts One-Time wachtwoordcodeaccounts

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C

Externe gebruikers kunnen nu E-mail en wachtwoordcodeaccounts One-Time gebruiken om zich aan te melden bij Azure AD 1e party- en LOB-apps. [Meer informatie](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Openbare preview- Beschikbaarheid van AD FS Sign-Ins in Azure AD

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Controle & rapportage
 
AD FS aanmeldingsactiviteit kan nu worden geïntegreerd met Azure AD-activiteitsrapportage, zodat u een uniforme weergave van de infrastructuur voor hybride identiteiten kunt bieden. Met behulp van het Azure AD Sign-Ins-rapport, Log Analytics en Azure Monitor Workbooks kunt u uitgebreide analyse uitvoeren voor zowel AAD- als AD FS-aanmeldingsscenario's, zoals AD FS-accountvergrendelingen, mislukte wachtwoordpogingen en pieken in onverwachte aanmeldingspogingen.

Ga voor meer informatie [AD FS aanmeldingen in Azure AD met Connect Health](../hybrid/how-to-connect-health-ad-fs-sign-in.md).

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Algemene beschikbaarheid - Gefaseerd implementatie naar cloudverificatie

**Type:** Nieuwe functie  
**Servicecategorie:** AD Connect  
**Productmogelijkheid:** Gebruikersverificatie
 
Gefaseerd implementatie naar cloudverificatie is nu algemeen beschikbaar. Met de gefaseerd implementatiefunctie kunt u groepen gebruikers selectief testen met cloudverificatiemethoden, zoals Passthrough Authentication (PTA) of Wachtwoord-hashsynchronisatie (PHS). Ondertussen blijven alle andere gebruikers in de federatief domeinen gebruikmaken van federatieservices, zoals AD FS of andere federatieservices om gebruikers te verifiëren. [Meer informatie](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Algemene beschikbaarheid: het kenmerk Gebruikerstype kan nu worden bijgewerkt in de Azure-beheerportal

**Type:** Nieuwe functie  
**Servicecategorie:** Gebruikerservaring en beheer  
**Productmogelijkheid:** Gebruikersbeheer
 
Klanten kunnen nu het gebruikerstype van Azure AD-gebruikers bijwerken wanneer ze hun gebruikersprofielgegevens bijwerken vanuit de Azure-beheerportal. Het gebruikerstype kan ook worden bijgewerkt vanuit Microsoft Graph. Zie Add [or update user profile information (Gebruikersprofielgegevens toevoegen of bijwerken) voor meer informatie.](active-directory-users-profile-azure-portal.md)
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Algemene beschikbaarheid - Replicasets voor Azure Active Directory Domain Services

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD Domain Services  
**Productmogelijkheid:** Azure AD Domain Services
 
De mogelijkheid van replicasets in Azure AD DS is nu algemeen beschikbaar. [Meer informatie](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Algemene beschikbaarheid: werk samen met uw partners met behulp van One-Time-wachtwoordcode in Azure Government cloud

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Organisaties in de Microsoft Azure Government cloud kunnen hun gasten nu in staat stellen uitnodigingen in te wisselen met e-mail One-Time wachtwoordcode. Dit zorgt ervoor dat gastgebruikers zonder Azure AD-, Microsoft- of Gmail-accounts in de Azure Government-cloud nog steeds kunnen samenwerken met hun partners door een tijdelijke code aan te vragen en in te voeren om zich aan te melden bij gedeelde resources. [Meer informatie](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - maart 2021

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden
 
In maart 2021 hebben we de volgende 37 nieuwe toepassingen toegevoegd in onze App-galerie met federatieondersteuning:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [LPH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Gedijen LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md) [,RAKH Spaces](https://ricohspaces.app/welcome), Saba [Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), Enterprise Vault.cloud voor [Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [](../saas-apps/evergreen-tutorial.md) [FAX. PLUS](../saas-apps/fax.plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen: https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - maart 2021

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [Door GitHub Enterprise beheerde gebruiker](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Introductie van MS Graph API voor de huisstijl van uw bedrijf

**Type:** Functie gewijzigd  
**Servicecategorie:** MS Graph  
**Productmogelijkheid:** B2B/B2C

[MS Graph API voor de huisstijl](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  is beschikbaar voor Azure AD of Microsoft 365 aanmeldingservaring om het beheer van de huisstijlparameters programmatisch toe te staan.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Algemene beschikbaarheid : eenmalige aanmelding op basis van headerverificatie met toepassingsproxy

**Type:** Functie gewijzigd  
**Servicecategorie:** App-proxy  
**Productmogelijkheid:** Access Control
 
Azure AD toepassingsproxy ondersteuning voor verificatie op basis van headers is nu algemeen beschikbaar. Met deze functie kunt u de gebruikerskenmerken configureren die zijn vereist als HTTP-headers voor de toepassing zonder dat er extra onderdelen nodig zijn om te implementeren. [Meer informatie](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Sms in twee punten voor MFA-server wordt niet meer ondersteund

**Type:** Afgekeurd  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 

Sms in twee punten voor de MFA-server is oorspronkelijk afgeschaft in 2018 en wordt na 24 februari 2021 niet meer ondersteund. Beheerders moeten een andere methode inschakelen voor gebruikers die nog steeds sms in twee punten gebruiken.

Op 8 december 2020 en 28 januari 2021 zijn Service Health e-mailmeldingen en meldingen van de Azure-portal verzonden naar de betrokken beheerders. De waarschuwingen zijn naar de RBAC-rollen Eigenaar, Mede-eigenaar, Beheerder en Servicebeheerder die aan de abonnementen zijn gekoppeld. [Meer informatie](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Februari 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Standaard vanaf oktober 2021 een e-mail verzenden voor verificatie van een een time-wachtwoordcode op

**Type:** Plannen voor wijziging  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 

Vanaf 31 oktober 2021 wordt Microsoft Azure Active Directory-e-mailverificatie met een een [time-time wachtwoordcode](../external-identities/one-time-passcode.md) de standaardmethode voor het uitnodigen van accounts en tenants voor B2B-samenwerkingsscenario's. Op dit moment staat Microsoft het inwisselen van uitnodigingen met niet-Azure Active Directory toe. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Niet-gevraagde, maar toestemming gegeven machtigingen worden niet meer toegevoegd aan tokens als deze voorwaardelijke toegang activeren

**Type:** Plannen voor wijziging  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Platform
 
Op dit moment krijgen toepassingen [die dynamische](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) machtigingen gebruiken alle machtigingen die ze hebben verleend voor toegang. Dit omvat toepassingen die niet zijn ingediend en zelfs als ze voorwaardelijke toegang activeren. Dit kan er bijvoorbeeld toe leiden dat een app die alleen aanvraagt en die ook toestemming heeft voor , wordt gedwongen om de voorwaardelijke toegang door te geven die is toegewezen `user.read` `files.read` voor de `files.read` machtiging. 

Om het aantal onnodige prompts voor voorwaardelijke toegang te verminderen, verandert Azure AD de manier waarop niet-gevraagde bereiken worden verstrekt aan toepassingen. Apps activeren alleen voorwaardelijke toegang voor machtigingen die ze expliciet aanvragen. Lees Wat is er nieuw [in verificatie voor meer informatie.](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Openbare preview- Een Tijdelijke toegangspas om referenties zonder wachtwoord te registreren

**Type:** Nieuwe functie  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection

Tijdelijke toegangspas is een tijdelijke wachtwoordcode die als sterke referenties fungeert en onboarding van referenties zonder wachtwoord en herstel mogelijk maakt wanneer een gebruiker de sterke verificatiefactor (bijvoorbeeld FIDO2-beveiligingssleutel of Microsoft Authenticator) heeft verloren of vergeten en zich moet aanmelden om nieuwe sterke verificatiemethoden te registreren. [Meer informatie](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Openbare preview- Aangemeld blijven (KMSI) in de volgende generatie gebruikersstromen

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C

De volgende generatie B2C-gebruikersstromen ondersteunt nu de [KMSI-functionaliteit (Aangemeld blijven)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) waarmee klanten de sessielevensduur voor de gebruikers van hun web- en systeemeigen toepassingen kunnen verlengen met behulp van een permanente cookie.  De functie houdt de sessie actief, zelfs wanneer de gebruiker de browser sluit en opnieuw opent, en wordt ingetrokken wanneer de gebruiker zich aftekent.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Openbare preview- External Identities Self-Service registreren in AAD met behulp van MSA-accounts

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Externe gebruikers kunnen nu Microsoft-accounts gebruiken om zich aan te melden bij de eerste partij van Azure AD en LOB-apps. [Meer informatie](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Openbare preview- Inwisselstatus voor een gastgebruiker opnieuw instellen

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Klanten kunnen nu bestaande externe gastgebruikers opnieuw aanstellen om hun inwisselstatus opnieuw in te stellen, waardoor het gastgebruikersaccount zonder dat ze toegang verliezen. [Meer informatie](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Openbare preview - API's voor /synchronisatie (inrichting) bieden nu ondersteuning voor toepassingsmachtigingen

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
Klanten kunnen nu application.readwrite.ownedby gebruiken als een toepassingsmachtiging voor het aanroepen van de synchronisatie-API's. Houd er rekening mee dat dit alleen wordt ondersteund voor het inrichten van Azure AD in toepassingen van derden (bijvoorbeeld AWS, gegevensstenen, enzovoort). Het wordt momenteel niet ondersteund voor HR-inrichting (Workday/Successfactors) of Cloud Sync (AD naar Azure AD). [Meer informatie](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Algemene beschikbaarheid : ingebouwde rol verificatiebeleidsbeheerder

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Gebruikers met deze rol kunnen het beleid voor verificatiemethoden, tenantbrede MFA-instellingen en wachtwoordbeveiligingsbeleid configureren. Deze rol verleent machtigingen voor het beheren van instellingen voor wachtwoordbeveiliging: configuraties voor slimme vergrendeling en het bijwerken van de aangepaste lijst met verboden wachtwoorden. [Meer informatie](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Algemene beschikbaarheid: gebruikersverzamelingen op Mijn apps zijn nu beschikbaar.

**Type:** Nieuwe functie  
**Servicecategorie:** Mijn apps  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
Gebruikers kunnen nu hun eigen groeperingen van apps maken op Mijn apps starter voor apps. Ze kunnen ook de volgorde wijzigen en verzamelingen verbergen die met hen zijn gedeeld door de beheerder. [Meer informatie](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Algemene beschikbaarheid - Automatisch invullen in Authenticator

**Type:** Nieuwe functie  
**Servicecategorie:** Microsoft Authenticator app  
**Productmogelijkheid:** Identity Security & Protection
 
Microsoft Authenticator biedt multi-factor authentication (MFA) en accountbeheermogelijkheden en vult nu ook automatisch wachtwoorden in op sites en apps die gebruikers op hun mobiele apparaat bezoeken (iOS en Android). 

Als u automatisch invullen in Authenticator wilt gebruiken, moeten gebruikers hun persoonlijke Microsoft-account aan Authenticator en deze gebruiken om hun wachtwoord te synchroniseren. Werk- of schoolaccounts kunnen op dit moment niet worden gebruikt om wachtwoorden te synchroniseren. [Meer informatie](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Algemene beschikbaarheid: interne gebruikers uitnodigen voor B2B-samenwerking

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Klanten kunnen nu interne gasten uitnodigen om B2B-samenwerking te gebruiken in plaats van een uitnodiging te verzenden naar een bestaand intern account. Hierdoor kunnen klanten de object-id, UPN, groepslidmaatschap en app-toewijzingen van die gebruiker behouden. [Meer informatie](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Algemene beschikbaarheid: ingebouwde rol van Domain Name Administrator

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Gebruikers met deze rol kunnen domeinnamen beheren (lezen, toevoegen, verifiëren, bijwerken en verwijderen). Ze kunnen ook mapinformatie over gebruikers, groepen en toepassingen lezen, omdat deze objecten domeinafhankelijkheden hebben. 

Voor on-premises omgevingen kunnen gebruikers met deze rol domeinnamen configureren voor federatie, zodat gekoppelde gebruikers altijd on-premises worden geverifieerd. Deze gebruikers kunnen zich vervolgens aanmelden bij op Azure AD gebaseerde services met hun on-premises wachtwoorden via een aanmelding. Federatie-instellingen moeten worden gesynchroniseerd via Azure AD Connect, zodat gebruikers ook machtigingen hebben voor het beheren van Azure AD Connect. [Meer informatie](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - februari 2021

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In februari 2021 hebben we de volgende 37 nieuwe toepassingen toegevoegd aan onze App-galerie met ondersteuning voor federatie:

[Loop Messenger-extensie](https://loopworks.com/loop-flow-messenger/), [Silver azure AD-adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- en Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tmabit IoT Bridge](https://bridge-us.tartabit.com/), [AKIDA](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot -](../saas-apps/parkalot-car-park-management-tutorial.md)Car park management , [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablonlon](../saas-apps/enablon-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen: https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - februari 2021

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Lees [Automate user provisioning to SaaS applications with Azure AD (Gebruikers inrichten voor SaaS-toepassingen](../app-provisioning/user-provisioning.md)automatiseren met Azure AD) voor meer informatie.

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Algemene beschikbaarheid- 10 Azure Active Directory nu hernoemd

**Type:** Functie gewijzigd  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
De naam van 10 ingebouwde Azure AD-rollen is gewijzigd, zodat deze zijn afgestemd op [de Microsoft 365-beheercentrum,](/microsoft-365/admin/microsoft-365-admin-center-preview) [De Azure AD-portal](https://portal.azure.com/) [en Microsoft Graph](https://developer.microsoft.com/graph/). Raadpleeg Beheerdersrolmachtigingen in Azure Active Directory voor meer informatie [over de Azure Active Directory.](../roles/permissions-reference.md#all-roles)

![Tabel met rolnamen in MS Graph API en de Azure Portal, en de voorgestelde definitieve naam voor API, Azure Portal en Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nieuwe huisstijl in gecombineerde MFA/SSPR-registratie

**Type:** Functie gewijzigd  
**Servicecategorie:** Gebruikerservaring en beheer  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
In het verleden werden bedrijfslogo's niet gebruikt op Azure Active Directory aanmeldingspagina's. De huisstijl van het bedrijf bevindt zich nu linksboven in de gecombineerde registratie van MFA/SSPR. De huisstijl van het bedrijf is ook opgenomen Sign-Ins de pagina Beveiligingsgegevens. [Meer informatie](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Algemene beschikbaarheid: manager op het tweede niveau kan worden ingesteld als alternatieve goedkeurder

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 
Een extra optie wanneer u goedkeurders selecteert, is nu beschikbaar in Rechtenbeheer. Als u Manager als goedkeurder selecteert als eerste goedkeurder, hebt u een andere optie, 'Manager op het tweede niveau als alternatieve goedkeurder', die u kunt kiezen in het veld Alternatieve goedkeurder. Als u deze optie selecteert, moet u een terugval goedkeurder toevoegen om de aanvraag door te sturen naar voor het geval het systeem de manager op het tweede niveau niet kan vinden. [Meer informatie](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Activiteitsdashboard voor verificatiemethoden

**Type:** Functie gewijzigd  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle van & rapportage
 

Het dashboard Activiteit van vernieuwde verificatiemethoden biedt beheerders een overzicht van de registratie van verificatiemethoden en gebruiksactiviteit in hun tenant. Het rapport geeft een overzicht van het aantal gebruikers dat is geregistreerd voor elke methode en ook welke methoden worden gebruikt tijdens het aanmelden en het opnieuw instellen van het wachtwoord. [Meer informatie](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Configureerbaarheid van vernieuwings- en sessietokenlevens in Configureerbare levensduur van token (CTL) wordt niet meer in gebruik genomen

**Type:** Afgekeurd  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Gebruikersverificatie
 
Configureerbaarheid van vernieuwings- en sessietokenlevens in CTL wordt ingetrokken. Azure Active Directory niet langer de configuratie van vernieuwings- en sessie-tokens in bestaande beleidsregels. [Meer informatie](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Januari 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Geheim token is een verplicht veld bij het configureren van de inrichting

**Type:** Plannen voor wijziging  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit

In het verleden kon het veld geheim token leeg worden gehouden bij het instellen van inrichting voor de aangepaste/BYOA-toepassing. Deze functie is uitsluitend bedoeld om te worden gebruikt voor testen. We werken de gebruikersinterface bij om het veld vereist te maken. 

Klanten kunnen deze vereiste voor testdoeleinden omdraaien met behulp van een functievlag in de browser-URL. [Meer informatie](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Openbare preview- - Gedeelde Android-apparaten aanpassen en configureren voor frontline workers op schaal

**Type:** Nieuwe functie  
**Servicecategorie:** Apparaatregistratie en -beheer  
**Productmogelijkheid:** Identity Security & Protection
 
Azure AD en Microsoft Endpoint Manager teams hebben gecombineerd om de mogelijkheid te bieden om uw frontline worker-apparaten aan te passen, te schalen en te beveiligen.

Met de volgende preview-mogelijkheden kunt u het volgende doen:
- Gedeelde Android-apparaten op schaal inrichten met Microsoft Endpoint Manager
- Uw toegang beveiligen voor shift-werknemers met voorwaardelijke toegang op basis van apparaten
- Aanmeldingservaringen voor de ploegendienst aanpassen met Managed Home Screen

Raadpleeg Aangepaste en geconfigureerde gedeelde apparaten voor [frontline workers op schaal voor meer informatie.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Openbare preview- Inrichtingslogboeken kunnen nu worden gedownload als een CSV of JSON

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Identity Lifecycle Management

Klanten kunnen de inrichtingslogboeken downloaden als een CSV- of JSON-bestand via de gebruikersinterface en via graph API. Raadpleeg [Provisioning reports in the Azure Active Directory portal (Rapporten inrichten in de Azure Active Directory portal) voor meer informatie.](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Openbare preview- Cloudgroepen toewijzen aan aangepaste Azure AD-rollen en rollen binnen het bereik van de beheereenheid

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Klanten kunnen een cloudgroep toewijzen aan aangepaste Azure AD-rollen of aan een rol binnen het bereik van een beheereenheid. Raadpleeg Cloudgroepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory voor meer informatie [over het gebruik van Azure Active Directory.](../roles/groups-concept.md)

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Algemene beschikbaarheid: Azure AD Connect cloudsynchronisatie (voorheen bekend als cloud-inrichting)

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD Connect cloudsynchronisatie  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
Azure AD Connect cloudsynchronisatie is nu algemeen beschikbaar voor alle klanten.

Azure AD Connect cloud verplaatst het zware werk van transformatielogica naar de cloud, waardoor uw on-premises footprint wordt verkleind. Daarnaast zijn er meerdere lichtgewicht agentimplementaties beschikbaar voor een hogere synchronisatiebeschikbaarheid. [Meer informatie](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Algemene beschikbaarheid: ingebouwde rollen voor Attack Simulation Administrator en Attack Payload Author

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Er zijn twee nieuwe rollen in Role-Based Access Control beschikbaar om toe te wijzen aan gebruikers, de auteur Van aanvalssimulatiebeheerder en Payload voor aanvallen. 

Gebruikers met de [rol Aanvalssimulatiebeheerder](../roles/permissions-reference.md#attack-simulation-administrator) hebben toegang tot alle simulaties in de tenant en kunnen:
- maken en beheren van alle aspecten van het maken van een aanvalssimulatie
- starten/plannen van een simulatie
-  simulatieresultaten bekijken. 

Gebruikers met de [rol Auteur van nettolading](../roles/permissions-reference.md#attack-payload-author) voor aanvallen kunnen nettoladingen voor aanvallen maken, maar ze niet daadwerkelijk starten of plannen. Nettoladingen van aanvallen zijn vervolgens beschikbaar voor alle beheerders in de tenant die deze kunnen gebruiken om een simulatie te maken.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Algemene beschikbaarheid: ingebouwde rol lezer van rapporten voor gebruiksoverzichten

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Gebruikers met de rol Rapport voor gebruiksoverzichten hebben toegang tot geaggregeerde gegevens op tenantniveau en bijbehorende inzichten in Microsoft 365-beheercentrum voor gebruiks- en productiviteitsscore. Ze hebben echter geen toegang tot details of inzichten op gebruikersniveau. 

In het Microsoft 365 voor de twee rapporten maken we onderscheid tussen geaggregeerde gegevens op tenantniveau en details op gebruikersniveau. Met deze rol wordt een extra beveiligingslaag toegevoegd aan de persoonlijke gegevens van de gebruiker. [Meer informatie](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Algemene beschikbaarheid: voorwaardelijke App-beveiliging in Azure AD vereisen

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection
 
Voorwaardelijke toegang van Azure AD voor App-beveiligingsbeleid vereisen is nu ga. 

Het beleid biedt de volgende mogelijkheden:
- Staat alleen toegang toe wanneer u een mobiele toepassing gebruikt die ondersteuning biedt voor Intune App-beveiliging
- Hiermee wordt alleen toegang gegeven wanneer een gebruiker een Intune-beveiligingsbeleid voor apps aan de mobiele toepassing heeft geleverd

Meer informatie over het instellen van beleid voor voorwaardelijke toegang voor app-beveiliging kunt u [hier vinden.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Algemene beschikbaarheid - E-One-Time wachtwoordcode

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Met E-mail-OTP kunnen organisaties over de hele wereld met iedereen samenwerken door een koppeling of uitnodiging via e-mail te verzenden. Uitgenodigde gebruikers kunnen hun identiteit verifiëren met de een time-time wachtwoordcode die naar hun e-mail wordt verzonden om toegang te krijgen tot de resources van hun partner. [Meer informatie](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - januari 2021

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Tijdelijke OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Zie Wat is geautomatiseerde gebruikers inrichten [van SaaS-apps in Azure AD? voor meer informatie.](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - januari 2021

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden

In januari 2021 hebben we de volgende 29 nieuwe toepassingen toegevoegd aan onze app-galerie met ondersteuning voor federatie:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue-sso](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO EMAIL](../saas-apps/ephoto-dam-tutorial.md), [Notion](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Naare Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [Onderdelen filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), User Experience [Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Openbare preview- Manager op het tweede niveau kan worden ingesteld als alternatieve goedkeurder

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 
Een extra optie wanneer u goedkeurders selecteert, is nu beschikbaar in Rechtenbeheer. Als u Manager als goedkeurder selecteert als eerste goedkeurder, hebt u een andere optie, 'Manager op het tweede niveau als alternatieve goedkeurder', die u kunt kiezen in het veld Alternatieve goedkeurder. Als u deze optie selecteert, moet u een terugval goedkeurder toevoegen om de aanvraag door te sturen naar voor het geval het systeem de manager op het tweede niveau niet kan vinden. [Meer informatie](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Algemene beschikbaarheid: ga rechtstreeks vanuit de Mijn toegang naar Teams

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 
U kunt Teams nu rechtstreeks vanuit de Mijn toegang starten. 

U doet dit door u aan te melden bij Mijn toegang ( , te navigeren naar Toegangspakketten en vervolgens naar het tabblad Actief te gaan om alle toegangspakketten weer te geven die u al https://myaccess.microsoft.com/) hebt. Wanneer u het geselecteerde toegangspakket uitv vouwt en de muisaanwijzer op Teams aanwijzen, kunt u het starten door op de knop Openen te klikken. [Meer informatie](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Verbeterde logboekregistratie & End-User voor riskante gastgebruikers

**Type:** Functie gewijzigd  
**Servicecategorie:** Identiteitsbeveiliging  
**Productmogelijkheid:** Identity Security & Protection
 

De prompts logboekregistratie en End-User voor riskante gastgebruikers zijn bijgewerkt. Meer informatie in [Identity Protection en B2B-gebruikers.](../identity-protection/concept-identity-protection-b2b.md)
 
---
 
## <a name="december-2020"></a>December 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Openbare preview- Azure AD B2C registreren en aanmelden via telefoon met ingebouwd beleid

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
Met B2C Phone Sign-up and Sign-in using Built-in Policy kunnen IT-beheerders en ontwikkelaars van organisaties hun eindgebruikers toestaan zich aan te melden en zich te registreren met een telefoonnummer in gebruikersstromen. Lees [Registreren en aanmelden via telefoon instellen voor gebruikersstromen (preview)](../../active-directory-b2c/phone-authentication-user-flows.md) voor meer informatie.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Algemene beschikbaarheid: standaardinstellingen voor beveiliging zijn nu standaard ingeschakeld voor alle nieuwe tenants

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Identity Security & Protection
 
Als u gebruikersaccounts wilt beveiligen, worden alle nieuwe tenants die zijn gemaakt op of na 12 november 2020, standaardinstellingen voor beveiliging ingeschakeld. Met Standaardinstellingen voor beveiliging worden meerdere beleidsregels afgedwongen, waaronder:
- Vereist dat alle gebruikers en beheerders zich registreren voor MFA met behulp van de Microsoft Authenticator App
- Vereist dat kritieke beheerdersrollen MFA gebruiken telkens wanneer ze zich aanmelden. Alle andere gebruikers wordt indien nodig om MFA gevraagd. 
- Verouderde verificatie wordt voor de hele tenant geblokkeerd. 

Lees Wat zijn standaardinstellingen voor [beveiliging? voor meer informatie.](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Algemene beschikbaarheid: ondersteuning voor groepen met maximaal 250.000 leden in AADConnect

**Type:** Functie gewijzigd  
**Servicecategorie:** AD Connect  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
Microsoft heeft een nieuw eindpunt (API) geïmplementeerd voor Azure AD Connect dat de prestaties verbetert van de synchronisatieservicebewerkingen voor Azure Active Directory. Wanneer u het nieuwe [V2-eindpunt gebruikt,](../hybrid/how-to-connect-sync-endpoint-api-v2.md)zult u merkbare prestatieverbeteringen ervaren bij het exporteren en importeren naar Azure AD. Dit nieuwe eindpunt ondersteunt de volgende scenario's:

- Groepen synchroniseren met maximaal 250.000 leden
- Prestatieverbeteringen bij exporteren en importeren in Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Algemene beschikbaarheid: rechtenbeheer beschikbaar voor tenants in de Azure China-cloud

**Type:** Nieuwe functie  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 

De mogelijkheden van Rechtenbeheer zijn nu beschikbaar voor alle tenants in de Azure China-cloud. Ga naar onze documentatiesite voor [identiteitsbeheer voor meer](https://docs.azure.cn/zh-cn/active-directory/governance/) informatie.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - december 2020

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Bizagi Studio voor digitale procesautomatisering](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - december 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden
 
In december 2020 hebben we de volgende 18 nieuwe toepassingen toegevoegd in onze App-galerie met federatieondersteuning:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softgift](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [federationage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Stockstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Navigeer rechtstreeks vanuit de Mijn toegang naar Teams

**Type:** Functie gewijzigd  
**Servicecategorie:** Productmogelijkheid **gebruikerstoegangsbeheer:** Rechtenbeheer

U kunt Teams nu rechtstreeks vanuit de Mijn toegang starten. U doet dit door u aan te melden [bij Mijn toegang](https://myaccess.microsoft.com/), naar  **Toegangspakketten** te gaan en vervolgens naar het tabblad Actief te gaan om alle toegangspakketten weer te geven waar u al toegang toe hebt. Wanneer u het toegangspakket uitv vouwt en de muisaanwijzer op Teams aanwijzen, kunt u het starten door op de knop **Openen te** klikken. 

Ga naar Toegang tot een toegangspakket aanvragen in Azure AD-rechtenbeheer voor meer informatie over het gebruik van de [Mijn toegang-portal.](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Openbare preview- Manager op het tweede niveau kan worden ingesteld als alternatieve goedkeurder

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer

Er is nu een extra optie beschikbaar in het goedkeuringsproces in Rechtenbeheer. Als u Manager selecteert als goedkeurder voor de eerste goedkeurder, hebt u een andere optie, Manager op het tweede niveau als alternatieve goedkeurder, die u kunt kiezen in het veld Alternatieve goedkeurder. Wanneer u deze optie selecteert, moet u een terugval goedkeurder toevoegen om de aanvraag naar door te sturen als het systeem de manager op het tweede niveau niet kan vinden.

Ga voor meer informatie naar [Goedkeuringsinstellingen wijzigen voor een toegangspakket in Azure AD-rechtenbeheer.](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)

--- 

## <a name="november-2020"></a>November 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory van TLS 1.0, TLS 1.1 en 3DES

**Type:** Plannen voor wijziging  
**Servicecategorie:** Alle Azure AD-toepassingen  
**Productmogelijkheid:** Normen

Azure Active Directory worden vanaf 30 juni 2021 de volgende protocollen in Azure Active Directory wereldwijde regio's afgeschaft:

- TLS 1.0
- TLS 1.1
- 3DES-coderingssuite (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Betrokken omgevingen zijn:
- Azure Commercial Cloud
- Office 365 GCC en WW

Gerelateerde aankondiging Alle combinaties van client-server en browserserver moeten gebruikmaken van TLS 1.2 en moderne coderingssuites om een beveiligde verbinding te onderhouden met Azure Active Directory voor Azure-, Office 365- en Microsoft 365-services. Deze wijziging heeft betrekking op [Azure Active Directory TLS 1.0 & 1.1 en 3DES Cipher Suite-afschaffing in US Gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

Raadpleeg Enable [support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (Ondersteuning voor TLS 1.2 inschakelen in](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)uw omgeving voor afschaffing van Azure AD TLS 1.1 en 1.0) voor richtlijnen voor het verwijderen van afgeschreven protocollen.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - november 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden

In november 2020 hebben we de volgende 52 nieuwe toepassingen toegevoegd in onze App-galerie met federatieondersteuning:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [Hebtloo](../saas-apps/tribeloo-tutorial.md), [Itslearning File Picker](https://pmteam.itslearning.com/) [,Ement Control](../saas-apps/crises-control-tutorial.md), [CourtAlert,](https://www.courtalert.com/) [StealthMail,](https://stealthmail.com/) [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), Virtual Risk [Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - Recruit](https://recruit.talview.com/login), Real Time Translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA Check-in](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), Lucid [(Alle producten)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), Nature [Research](../saas-apps/nature-research-tutorial.md), Play [Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® door eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Openbare preview - Aangepaste rollen voor bedrijfsapps

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
 [Aangepaste RBAC-rollen voor gedelegeerd toepassingsbeheer voor ondernemingen](../roles/custom-available-permissions.md) zijn nu beschikbaar als openbare preview. Deze nieuwe machtigingen zijn gebaseerd op de aangepaste rollen voor het beheer van app-registratie, waarmee u op een fijner manier kunt bepalen welke toegang uw beheerders hebben. Na een periode worden extra machtigingen voor het delegeren van beheer van Azure AD vrijgegeven.

Enkele veelvoorkomende overdrachtsscenario's:
- toewijzing van gebruikers en groepen die toegang hebben tot op SAML gebaseerde toepassingen voor een aanmelding
- het maken van toepassingen in de Azure AD-galerie
- Standaard SAML-configuraties voor op SAML gebaseerde toepassingen voor een een aantal aanmeldingen bijwerken en lezen
- beheer van ondertekeningscertificaten voor op SAML gebaseerde toepassingen voor een aanmelding
- e-mailadressen van e-mailadressen voor meldingen van verlopende aanmeldingscertificaten voor op SAML gebaseerde toepassingen voor een aantal aanmeldingen
- update van het SAML-tokenhandtekening- en aanmeldingsalgoritme voor op SAML gebaseerde toepassingen voor een aanmelding
- maken, verwijderen en bijwerken van gebruikerskenmerken en -claims voor op SAML gebaseerde toepassingen voor een aanmelding
- mogelijkheid om inrichtingstaken in te schakelen, uit te schakelen en opnieuw te starten
- updates voor kenmerktoewijzing
- mogelijkheid om inrichtingsinstellingen te lezen die zijn gekoppeld aan het object
- de mogelijkheid om inrichtingsinstellingen te lezen die zijn gekoppeld aan uw service-principal
- mogelijkheid om toepassingstoegang te autoreren voor inrichting

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Openbare preview- Azure AD toepassingsproxy biedt standaard ondersteuning voor toegang tot een aanmelding voor toepassingen die headers gebruiken voor verificatie

**Type:** Nieuwe functie  
**Servicecategorie:** App-proxy  
**Productmogelijkheid:** Access Control
 
Azure Active Directory (Azure AD) biedt toepassingsproxy ondersteuning voor toegang tot toepassingen die gebruikmaken van headers voor verificatie. U kunt headerwaarden configureren die zijn vereist voor uw toepassing in Azure AD. De headerwaarden worden naar de toepassing verzonden via toepassingsproxy. Zie [Header-based single sign-on for on-premises apps with Azure AD-app Proxy (Header-based single sign-on voor on-premises apps met Azure AD-app Proxy) voor meer informatie.](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Algemene beschikbaarheid: Azure AD B2C registreren en aanmelden via telefoon met aangepast beleid

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C

Met het registreren en aanmelden van een telefoonnummer kunnen ontwikkelaars en ondernemingen hun klanten toestaan zich te registreren en zich aan te melden met een een keer wachtwoord dat via sms naar het telefoonnummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook het telefoonnummer wijzigen als deze geen toegang meer heeft tot zijn telefoon. Met de kracht van aangepast beleid kunnen ontwikkelaars en ondernemingen hun merk communiceren via paginaaanpassing. Meer informatie over het [instellen van telefonische aanmelding](../../active-directory-b2c/phone-authentication-user-flows.md)en aanmelding met aangepast beleid vindt u in Azure AD B2C .
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - november 2020

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD (Gebruikers inrichten voor SaaS-toepassingen](../app-provisioning/user-provisioning.md)automatiseren met Azure AD) voor meer informatie.
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Openbare preview- E-Sign-In met ProxyAddresses nu implementeerbaar via gefaseerd implementeren

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
Tenantbeheerders kunnen nu Gefaseerd implementeren om e-mailgroepen Sign-In ProxyAddresses te implementeren in specifieke Azure AD-groepen. Dit kan helpen bij het proberen van de functie voordat u deze implementeert in de hele tenant via het beleid voor thuis realmdetectie. Instructies voor het implementeren van e-Sign-In met ProxyAddresses via gefaseerd implementeren staan in de [documentatie](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Beperkte preview- Diagnostische gegevens voor aanmelden

**Type:** Nieuwe functie  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle & rapportage
 
Met de eerste preview-versie van de diagnostische aanmeldingsgegevens kunnen beheerders nu aanmeldingen van gebruikers controleren. Beheerders kunnen contextuele, specifieke en relevante informatie en richtlijnen ontvangen over wat er is gebeurd tijdens een aanmelding en hoe ze problemen kunnen oplossen. De diagnostische gegevens zijn beschikbaar op zowel Het Azure AD-niveau als de blades Diagnose en Oplossen voor voorwaardelijke toegang. De diagnostische scenario's die in deze release worden behandeld, zijn voorwaardelijke toegang, Multi-Factor Authentication en een geslaagde aanmelding.

Zie Wat is diagnostische gegevens over [aanmelden in Azure AD? voor meer informatie.](../reports-monitoring/overview-sign-in-diagnostics.md)
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Verbeterde eigenschappen voor onbekende aanmeldingen

**Type:** Functie gewijzigd  
**Servicecategorie:** Identiteitsbeveiliging  
**Productmogelijkheid:** Identity Security & Protection

  Onbekende aanmeldingseigenschappen detecties is bijgewerkt. Klanten merken mogelijk detecties van onbekende aanmeldingseigenschappen met een hoog risico. Zie Wat [is risico? voor meer informatie.](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Vernieuwing van de openbare preview van de cloud inrichtingsagent is nu beschikbaar (versie: 1.1.281.0)

**Type:** Functie gewijzigd  
**Servicecategorie:** Azure AD-cloud inrichten  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
De inrichtingsagent voor de cloud is uitgebracht in openbare preview en is nu beschikbaar via de portal. Deze release bevat verschillende verbeteringen, waaronder ondersteuning voor GMSA voor uw domeinen, die betere beveiliging, verbeterde initiële synchronisatiecycli en ondersteuning voor grote groepen biedt. Bekijk de versiegeschiedenis van [de](../app-provisioning/provisioning-agent-release-version-history.md) release voor meer informatie. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker-herstelsleutel-API-eindpunt nu onder /informationProtection

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van apparaattoegang  
**Productmogelijkheid:** Levenscyclusbeheer van apparaten
 
Voorheen kon u BitLocker-sleutels herstellen via het /bitlocker-eindpunt. Dit eindpunt wordt uiteindelijk afgeschaft en klanten moeten de API gaan gebruiken die nu onder /informationProtection valt. 

Zie [BitLocker-herstel-API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) voor updates van de documentatie om deze wijzigingen weer te geven.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Algemene beschikbaarheid van toepassingsproxy-ondersteuning voor Extern bureaublad-services HTML5-webclient

**Type:** Functie gewijzigd  
**Servicecategorie:** App-proxy  
**Productmogelijkheid:** Access Control
 
Azure AD toepassingsproxy ondersteuning voor Extern bureaublad-services (RDS) Web Client is nu algemeen beschikbaar. Met de RDS-webclient hebben gebruikers toegang tot de Extern bureaublad-infrastructuur via een browser die geschikt is voor HTLM5, zoals Microsoft Edge, Internet Explorer 11, Google Chrome, en meer. Gebruikers kunnen vanaf elke locatie met externe apps of bureaubladen werken zoals met een lokaal apparaat. 

Met behulp van Azure AD toepassingsproxy kunt u de beveiliging van uw RDS-implementatie verhogen door pre-verificatie en beleid voor voorwaardelijke toegang af te afdwingen voor alle typen uitgebreide client-apps. Zie Publish [Extern bureaublad with Azure AD toepassingsproxy (Extern bureaublad publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nieuwe verbeterde dynamische groepsservice is beschikbaar als openbare preview

**Type:** Functie gewijzigd  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheid:** Samenwerking
 
Verbeterde dynamische groepsservice is nu beschikbaar als openbare preview. Nieuwe klanten die dynamische groepen in hun tenants maken, maken gebruik van de nieuwe service. De tijd die nodig is om een dynamische groep te maken, is evenredig met de grootte van de groep die wordt gemaakt in plaats van de grootte van de tenant. Deze update verbetert de prestaties voor grote tenants aanzienlijk wanneer klanten kleinere groepen maken. 

De nieuwe service is ook bedoeld om lidoptellingen en -verwijderingen binnen enkele minuten te voltooien vanwege kenmerkwijzigingen. Enkelvoudige verwerkingsfouten blokkeren ook de verwerking van tenants niet. Zie onze documentatie voor meer informatie over het maken van dynamische [groepen.](../enterprise-users/groups-create-rule.md)
 
---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>On-premises hybride Azure AD-agents die worden beïnvloed door wijzigingen in Azure TLS-certificaten

**Type:** Plannen voor wijziging  
**Servicecategorie:** N/a  
**Productmogelijkheid:** Platform

Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze update wordt veroorzaakt doordat de huidige CA-certificaten niet voldoen aan een van de ca/browser forum basislijnvereisten. Deze wijziging is van invloed op hybride Azure AD-agents die on-premises zijn geïnstalleerd en omgevingen hebben met een vaste lijst met basiscertificaten en moeten worden bijgewerkt om de nieuwe certificaatverkenders te vertrouwen.

Deze wijziging leidt tot een onderbreking van de service als u niet onmiddellijk actie onderneemt. Deze agents omvatten [toepassingsproxy-connectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) voor externe toegang tot on-premises, [Passthrough-verificatieagents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) waarmee uw gebruikers zich kunnen aanmelden bij toepassingen met dezelfde wachtwoorden en [Cloud Provisioning Preview-agents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) die AD-synchronisatie met Azure AD uitvoeren. 

Als u een omgeving hebt waarin firewallregels zijn ingesteld om alleen uitgaande aanroepen naar specifieke CRL-download (Certificate Revocation List) toe te staan, moet u de volgende CRL- en OCSP-URL's toestaan. Zie Azure TLS-certificaatwijzigingen voor meer informatie over de wijziging en de CRL- en OCSP-URL's om toegang tot [in te stellen.](../../security/fundamentals/tls-certificate-changes.md)

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Inrichtingsgebeurtenissen worden verwijderd uit auditlogboeken en alleen gepubliceerd naar inrichtingslogboeken

**Type:** Plannen voor wijziging  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle van & rapportage
 
Activiteit van de [SCIM-inrichtingsservice](../app-provisioning/user-provisioning.md) wordt vastgelegd in zowel de auditlogboeken als de inrichtingslogboeken. Dit omvat activiteiten zoals het maken van een gebruiker in ServiceNow, groep in GSuite of het importeren van een rol uit AWS. In de toekomst worden deze gebeurtenissen alleen gepubliceerd in de inrichtingslogboeken. Deze wijziging wordt geïmplementeerd om dubbele gebeurtenissen in logboeken en extra kosten te voorkomen die worden gemaakt door klanten die de logboeken in Log Analytics gebruiken. 

Er wordt een update verstrekt wanneer een datum is voltooid. Deze afschaffing is niet gepland voor het kalenderjaar 2020. 

> [!NOTE]
> Dit heeft geen invloed op gebeurtenissen in de auditlogboeken buiten de synchronisatiegebeurtenissen die door de inrichtingsservice worden ingediend. Gebeurtenissen zoals het maken van een toepassing, beleid voor voorwaardelijke toegang, een gebruiker in de map, enzovoort, worden nog steeds in de auditlogboeken ingediend. [Meer informatie](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>On-premises hybride Azure AD-agents die worden beïnvloed door wijzigingen in het Azure Transport Layer Security-certificaat (TLS)

**Type:** Plannen voor wijziging  
**Servicecategorie:** N/a  
**Productmogelijkheid:** Platform
 
Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Er wordt een update weergegeven vanwege de huidige CA-certificaten die niet voldoen aan een van de basislijnvereisten voor CA/Browser-forum. Deze wijziging is van invloed op hybride Azure AD-agents die on-premises zijn geïnstalleerd met geharde omgevingen met een vaste lijst met basiscertificaten. Deze agents moeten worden bijgewerkt om de nieuwe certificaatverwerkers te vertrouwen.

Deze wijziging leidt tot onderbreking van de service als u niet onmiddellijk actie onderneemt. Deze agents zijn onder andere: 
- toepassingsproxy voor externe toegang tot on-premises [connectors](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- [Passthrough-verificatieagents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) waarmee uw gebruikers zich met dezelfde wachtwoorden kunnen aanmelden bij toepassingen
- [Cloud provisioning Preview-agents](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) die AD naar Azure AD-synchronisatie uitvoeren. 

Als u een omgeving hebt waarin firewallregels zijn ingesteld om alleen uitgaande aanroepen naar specifieke CRL-download (Certificate Revocation List) toe te staan, moet u CRL- en OCSP-URL's toestaan. Zie Wijzigingen in Azure TLS-certificaat voor volledige informatie over de wijziging en de CRL- en OCSP-URL's om toegang tot [in te stellen.](../../security/fundamentals/tls-certificate-changes.md)
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory afschaffing van TLS 1.0, TLS 1.1 en 3DES in US Gov Cloud

**Type:** Plannen voor wijziging  
**Servicecategorie:** Alle Azure AD-toepassingen  
**Productmogelijkheid:** Normen
 
Azure Active Directory worden de volgende protocollen afgeschaft vanaf 31 maart 2021:
- TLS 1.0
- TLS 1.1
- 3DES-coderingssuite (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Alle combinaties van client-server en browserserver moeten gebruikmaken van TLS 1.2 en moderne coderingssuites om een beveiligde verbinding te onderhouden met Azure Active Directory voor Azure-, Office 365- en Microsoft 365-services.

Betrokken omgevingen zijn:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Raadpleeg Enable [support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (Ondersteuning voor TLS 1.2 inschakelen in](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)uw omgeving voor afschaffing van Azure AD TLS 1.1 en 1.0) voor hulp bij het verwijderen van afhankelijkheden van afgeschafte protocollen.
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Toepassingen toewijzen aan rollen in beheereenheid en objectbereik

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Met deze functie kunt u een toepassing (SPN) toewijzen aan een beheerdersrol voor het bereik van de beheereenheid. Raadpleeg Scoped rollen toewijzen aan een [beheereenheid voor meer informatie.](../roles/admin-units-assign-roles.md)

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Nu kunt u gastgebruikers uitschakelen en verwijderen wanneer de toegang tot een resource wordt geweigerd

**Type:** Nieuwe functie  
**Servicecategorie:** Toegangsbeoordelingen  
**Productmogelijkheid:** Identiteitsbeheer
 
Uitschakelen en verwijderen is een geavanceerd besturingselement in Azure AD-toegangsbeoordelingen om organisaties te helpen bij het beter beheren van externe gasten in groepen en apps. Als gasten tijdens een toegangsbeoordeling worden geweigerd, wordt **het** aanmelden voor gasten na 30 dagen automatisch geblokkeerd door uitschakelen en verwijderen. Na 30 dagen worden ze helemaal verwijderd uit de tenant.

Zie Externe identiteiten uitschakelen en verwijderen met [Azure AD-toegangsbeoordelingen](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)voor meer informatie over deze functie.
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Makers van toegangsbeoordelingen kunnen aangepaste berichten in e-mailberichten toevoegen aan revisoren

**Type:** Nieuwe functie  
**Servicecategorie:** Toegangsbeoordelingen  
**Productmogelijkheid:** Identiteitsbeheer
 
In Azure AD-toegangsbeoordelingen kunnen beheerders die beoordelingen maken nu een aangepast bericht naar de revisoren schrijven. Revisoren zien het bericht in de e-mail die ze ontvangen waarin hen wordt gevraagd de beoordeling te voltooien. Zie stap 14 van de sectie Een of meer toegangsbeoordelingen maken voor meer informatie over het gebruik [van deze](../governance/create-access-review.md#create-one-or-more-access-reviews) functie.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - oktober 2020

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integratieassistent voor Azure AD B2C

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
De integratieassistent (preview) is nu beschikbaar voor Azure AD B2C App-registraties. Deze ervaring helpt u bij het configureren van uw toepassing voor algemene scenario's.. Meer informatie over best practices en aanbevelingen [voor Het Microsoft Identity Platform.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Rolsjabloon-id weergeven in Azure Portal UI

**Type:** Nieuwe functie  
**Servicecategorie:** Azure-rollen  
**Productmogelijkheid:** Access Control
 

U kunt nu de sjabloon-id van elke Azure AD-rol in de Azure Portal. Selecteer in Azure AD de  **beschrijving** van de geselecteerde rol. 

Het wordt aanbevolen dat klanten rolsjabloon-ID's in hun PowerShell-script en -code gebruiken in plaats van de weergavenaam. Rolsjabloon-id wordt ondersteund voor gebruik [met directoryRollen](/graph/api/resources/directoryrole) en [roleDefinition-objecten.](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) Zie Ingebouwde Azure AD-rollen voor meer informatie over [rolsjabloon-ID's.](../roles/permissions-reference.md)

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>API-connectors voor Azure AD B2C registreren van gebruikersstromen is nu beschikbaar als openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 

API-connectors zijn nu beschikbaar voor gebruik met Azure Active Directory B2C. Met API-connectors kunt u web-API's gebruiken om uw gebruikersstromen voor aanmelding aan te passen en te integreren met externe cloudsystemen. U kunt API-connectors gebruiken voor het volgende:

- Integreren met aangepaste goedkeuringswerkstromen
- Gebruikersinvoergegevens valideren
- Gebruikerskenmerken overschrijven 
- Aangepaste bedrijfslogica uitvoeren 

 Ga naar [de documentatie API-connectors gebruiken om](../../active-directory-b2c/api-connectors-overview.md) de aanmelding aan te passen en uit te breiden voor meer informatie.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Status-eigenschap voor verbonden organisaties in rechtenbeheer

**Type:** Nieuwe functie  
**Servicecategorie:** Directory **Management-productmogelijkheid:** Rechtenbeheer
 

 Alle verbonden organisaties hebben nu een extra eigenschap met de naam 'Status'. De status controleert hoe de verbonden organisatie wordt gebruikt in beleidsregels die verwijzen naar 'alle geconfigureerde verbonden organisaties'. De waarde wordt 'geconfigureerd' (wat betekent dat de organisatie binnen het bereik valt van beleidsregels die gebruikmaken van de component 'all' of 'voorgesteld' (wat betekent dat de organisatie zich niet binnen het bereik van de organisatie heeft).  

Handmatig gemaakte verbonden organisaties hebben de standaardinstelling 'geconfigureerd'. In de tussentijd worden automatisch gemaakte (gemaakt via beleidsregels waarmee een gebruiker van internet toegang kan aanvragen) standaard ingesteld op 'voorgesteld'.  Alle verbonden organisaties die zijn gemaakt vóór 9 september 2020, worden ingesteld op 'geconfigureerd'. Beheerders kunnen deze eigenschap zo nodig bijwerken. [Meer informatie](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory External Identities heeft nu premium geavanceerde beveiligingsinstellingen voor B2C

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
Functies voor voorwaardelijke toegang en risicodetectie op basis van risico's van Identity Protection zijn nu beschikbaar in [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Met deze geavanceerde beveiligingsfuncties kunnen klanten nu:
- Maak gebruik van intelligente inzichten om risico's te beoordelen met B2C-apps en eindgebruikersaccounts. Detecties omvatten atypical travel, anonieme IP-adressen, aan malware gekoppelde IP-adressen en Azure AD-bedreigingsinformatie. Portal- en API-rapporten zijn ook beschikbaar.
- Risico's automatisch aanpakken door adaptieve verificatiebeleidsregels te configureren voor B2C-gebruikers. App-ontwikkelaars en -beheerders kunnen realtime-risico's beperken door meervoudige verificatie (MFA) te vereisen of toegang te blokkeren, afhankelijk van het gedetecteerde gebruikersrisiconiveau, met extra besturingselementen die beschikbaar zijn op basis van locatie, groep en app.
- Integreren met Azure AD B2C gebruikersstromen en aangepaste beleidsregels. Voorwaarden kunnen worden geactiveerd vanuit ingebouwde gebruikersstromen in Azure AD B2C of kunnen worden opgenomen in aangepast B2C-beleid. Net als bij andere aspecten van de B2C-gebruikersstroom kunnen berichten over de eindgebruikerservaring worden aangepast. Aanpassing is afhankelijk van de stem, het merk en de oplossingen van de organisatie.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - oktober 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden
 
In oktober 2020 hebben we de volgende 27 nieuwe toepassingen toegevoegd in onze App-galerie met ondersteuning voor federatie:

[Sentry](../saas-apps/sentry-tutorial.md) [,Blebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), Zone Technologies [Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckCheck](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Appointment Herinnering](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), Loop Flow [CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Inrichtingslogboeken kunnen nu worden gestreamd naar Log Analytics

**Type:** Nieuwe functie  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle & rapportage
 

Publiceer uw inrichtingslogboeken naar Log Analytics om het volgende te doen:
- Inrichtingslogboeken langer dan 30 dagen opslaan
- Aangepaste waarschuwingen en meldingen definiëren
- Dashboards bouwen om de logboeken te visualiseren
- Complexe query's uitvoeren om de logboeken te analyseren 

Zie Begrijpen hoe inrichting kan worden geïntegreerd met Azure Monitor logboeken voor meer informatie [Azure Monitor gebruiken.](../app-provisioning/application-provisioning-log-analytics.md)
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Inrichtingslogboeken kunnen nu worden bekeken door toepassingseigenaren

**Type:** Functie gewijzigd  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle & rapportage
 
U kunt eigenaren van toepassingen nu toestaan de activiteiten van de inrichtingsservice te bewaken en problemen op te lossen zonder hen een bevoorrechte rol te geven of it een knelpunt te maken. [Meer informatie](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>De naam van tien Azure Active Directory wijzigen

**Type:** Functie gewijzigd  
**Servicecategorie:** Azure-rollen  
**Productmogelijkheid:** Access Control
 
Sommige Azure Active Directory (AD) ingebouwde rollen hebben namen die verschillen van de rollen die worden weergegeven in Microsoft 365-beheercentrum, de Azure AD-portal en Microsoft Graph. Deze inconsistentie kan problemen veroorzaken in geautomatiseerde processen. Met deze update wijzigen we de naam van 10 rolnamen om ze consistent te maken. De volgende tabel heeft de nieuwe rolnamen:

![Tabel met rolnamen in MS Graph API en de Azure Portal, en de voorgestelde nieuwe rolnaam in M365-beheercentrum, Azure Portal en API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C ondersteuning voor auth-codestroom voor SPA's met MSAL JS 2.x

**Type:** Functie gewijzigd  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
MSAL.js versie 2.x bevat nu ondersteuning voor de autorisatiecodestroom voor web-apps met één pagina (SPA's). Azure AD B2C biedt nu ondersteuning voor het gebruik van het type spa-app op de Azure Portal en het gebruik van MSAL.js autorisatiecodestroom met PKCE voor apps met één pagina. Hierdoor kunnen SPA's die gebruikmaken Azure AD B2C eenmalige aanmelding met nieuwere browsers onderhouden en zich houden aan nieuwere aanbevelingen voor verificatieprotocollen. Ga aan de slag met de zelfstudie Een toepassing [met één pagina registreren (SPA) in Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) zelfstudie.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Updates voor het onthouden van Multi-Factor Authentication (MFA) op een vertrouwd apparaat

**Type:** Functie gewijzigd  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 

We hebben de functie [Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) onthouden op een vertrouwd apparaat onlangs bijgewerkt om de verificatie met maximaal 365 dagen uit te breiden. Azure Active Directory (Azure AD) [Premium-licenties](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) kunnen ook gebruikmaken van het beleid Voorwaardelijke toegang – Frequentie van aanmelding, dat meer flexibiliteit biedt voor instellingen voor opnieuw autorisatie.

Voor een optimale gebruikerservaring raden we u aan om de aanmeldingsfrequentie voor voorwaardelijke toegang te gebruiken om de levensduur van sessies op vertrouwde apparaten, locaties of sessies met een laag risico te verlengen als alternatief voor MFA onthouden op een vertrouwd apparaat. Lees onze meest recente richtlijnen voor het optimaliseren van de ervaring voor [opnieuw autorisatie](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)om aan de slag te gaan.

---
