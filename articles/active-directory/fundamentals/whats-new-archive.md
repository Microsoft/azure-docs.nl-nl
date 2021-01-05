---
title: Wilt u archiveren wat er nieuw is in Azure Active Directory? | Microsoft Docs
description: De opmerkingen bij de release van wat is er nieuw in het gedeelte Overzicht van deze inhoudsset, bevat zes maanden aan activiteit. Na zes maanden worden de items uit het hoofd artikel verwijderd en in dit archief artikel geplaatst.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e90adcb5312df6e1dfe6d4b58407edeb924a8809
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862437"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Wilt u archiveren wat er nieuw is in Azure Active Directory?

De belangrijkste [nieuwe functies in azure Active Directory? release opmerkingen](whats-new.md) artikel bevat updates voor de afgelopen zes maanden, terwijl dit artikel alle oudere informatie bevat.

Wat is er nieuw in Azure Active Directory? release opmerkingen bevatten informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

---
## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Gebruikers risico voorwaarde in beleid voor voorwaardelijke toegang

**Type:** Plan voor wijziging  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

Met de ondersteuning voor gebruikers Risico's in het beleid voor voorwaardelijke toegang van Azure AD kunt u meerdere beleids regels voor gebruikers risico maken. Er zijn verschillende minimale gebruikers risico niveaus vereist voor verschillende gebruikers en apps. Op basis van het risico van gebruikers kunt u beleids regels maken om de toegang te blok keren, multi-factor Authentication, een beveiligd wacht woord wijzigen of omleiden naar Microsoft Cloud App Security om sessie beleid af te dwingen, zoals aanvullende controle.

Voor de gebruikers risico voorwaarde is Azure AD Premium P2 vereist, omdat het gebruikmaakt van Azure Identity Protection, een P2-aanbieding. Raadpleeg de [documentatie voor voorwaardelijke toegang van Azure AD](../conditional-access/index.yml)voor meer informatie over voorwaardelijke toegang.

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO ondersteunt nu apps waarvoor SPNameQualifier moet worden ingesteld wanneer daarom wordt gevraagd

**Type:** Vaste  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Voor sommige SAML-toepassingen moet SPNameQualifier worden geretourneerd in de bewering wanneer daarom wordt gevraagd. Azure AD reageert nu correct wanneer er een SPNameQualifier is aangevraagd in het beleid voor NameID van aanvragen. Dit geldt ook voor door SP geïnitieerde aanmelding en de IdP-aanmelding wordt gevolgd.  Zie [Single Sign-On SAML protocol](../develop/single-sign-on-saml-protocol.md)(Engelstalig) voor meer informatie over het SAML-protocol in azure Active Directory.

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B-samen werking biedt ondersteuning voor het uitnodigen van MSA-en Google-gebruikers in Azure Government tenants

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

Azure Government tenants die gebruikmaken van de B2B-samenwerkings functies kunnen nu gebruikers uitnodigen met een micro soft-of Google-account. Als u wilt weten of uw Tenant deze mogelijkheden kan gebruiken, volgt u de instructies op [Hoe kan ik zien of B2B-samen werking beschikbaar is in mijn Azure US Government-Tenant?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Gebruikers object in MS Graph v1 bevat nu de eigenschappen externalUserState en externalUserStateChangedDateTime

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 

De eigenschappen externalUserState en externalUserStateChangedDateTime kunnen worden gebruikt om uitgenodigde B2B-gasten te vinden die hun uitnodigingen nog niet hebben geaccepteerd, evenals het bouwen van automatisering, zoals het verwijderen van gebruikers die hun uitnodigingen na een aantal dagen niet hebben geaccepteerd. Deze eigenschappen zijn nu beschikbaar in MS Graph v1. Raadpleeg het [resource type](/graph/api/resources/user)van de gebruiker voor hulp bij het gebruik van deze eigenschappen.
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Verificatie sessies beheren in azure AD voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Met de beheer mogelijkheden voor verificatie sessies kunt u configureren hoe vaak uw gebruikers aanmeldings referenties moeten opgeven en of ze referenties moeten opgeven na het sluiten en opnieuw openen van browsers om meer beveiliging en flexibiliteit in uw omgeving te bieden.
 
Daarnaast wordt het beheer van verificatie sessies alleen toegepast op de eerste factor Authentication op Azure AD join, hybride Azure AD toegevoegd en geregistreerde Azure AD-apparaten. Verificatie sessie beheer is nu ook van toepassing op MFA. Zie [verificatie sessie beheer met voorwaardelijke toegang configureren](../conditional-access/howto-conditional-access-session-lifetime.md)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-juni 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juni 2020 zijn de volgende 29 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [mailgates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [gate keeper](https://www.gatekeeperhq.com/), [hub planner](../saas-apps/hub-planner-tutorial.md), [Ansira-partner go-to-Market-werkset](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kisi fysieke beveiliging](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [Zero](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX voor bedrijven SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [veld-id](../saas-apps/field-id-tutorial.md), [curriculum SAML](../saas-apps/curricula-saml-tutorial.md) [,](https://smallstep.com/sso-ssh/) [Perforce Helix core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance](https://cloud.metacompliance.com/)  

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial . Raadpleeg de volgende informatie voor het weer geven van uw toepassing in de app-galerie van Azure AD: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-connectors voor externe identiteiten selfservice aanmelding is nu beschikbaar als open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Met API-connectors voor externe identiteiten kunt u gebruikmaken van web-Api's voor het integreren van self-service registratie met externe Cloud systemen. Dit betekent dat u nu web-Api's kunt aanroepen als specifieke stappen in een registratie stroom voor het activeren van op de cloud gebaseerde aangepaste werk stromen. U kunt bijvoorbeeld API-connectors gebruiken voor het volgende:

- Integreren met een aangepaste goedkeurings werk stroom.
- Identiteits controle uitvoeren
- Gebruikers invoer gegevens valideren
- Gebruikers kenmerken overschrijven
- Aangepaste bedrijfs logica uitvoeren

Zie [API-connectors gebruiken voor het aanpassen en uitbreiden van self-service registratie](../external-identities/api-connectors-overview.md), of [het aanpassen van externe identiteiten selfservice aanmelding met Web API-integraties](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)voor meer informatie over alle mogelijkheden van API-connectors.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>On-demand inrichten en gebruikers in een paar seconden in uw apps ophalen

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service werkt momenteel op cyclische basis. De service wordt elke 40 minuten uitgevoerd. Met de [functie voor inrichting op aanvraag](https://aka.ms/provisionondemand) kunt u een gebruiker kiezen en deze in enkele seconden inrichten. Met deze mogelijkheid kunt u snel inrichtings problemen oplossen zonder opnieuw op te starten om ervoor te zorgen dat de inrichtings cyclus opnieuw wordt gestart. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nieuwe machtiging voor het gebruik van het beheer van rechten van Azure AD in Graph

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten
 
Er is een nieuwe gedelegeerde machtiging EntitlementManagement. Read. all is nu beschikbaar voor gebruik met de API voor rechten beheer in Microsoft Graph bèta. Zie [werken met de Azure AD-API voor rechten beheer](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)voor meer informatie over de beschik bare api's.

---

### <a name="identity-protection-apis-available-in-v10"></a>Api's voor identiteits beveiliging die beschikbaar zijn in v 1.0

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De riskyUsers-en riskDetections-Microsoft Graph Api's zijn nu algemeen beschikbaar. Nu ze beschikbaar zijn op het eind punt van de v 1.0, nodigen we u uit om ze in productie te gebruiken. Raadpleeg de [Microsoft Graph-documenten](/graph/api/resources/identityprotectionroot)voor meer informatie.
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Gevoeligheids labels voor het Toep assen van beleid op Microsoft 365 groepen is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking
 

U kunt nu gevoeligheids labels maken en de label instellingen gebruiken om beleid toe te passen op Microsoft 365 groepen, waaronder privacy (openbaar of privé) en beleid voor externe gebruikers toegang. U kunt een label met het privacybeleid persoonlijk maken en beleid voor externe gebruikers toegang om gast gebruikers niet toe te staan. Wanneer een gebruiker dit label toepast op een groep, is de groep privé en kunnen gast gebruikers niet aan de groep worden toegevoegd. 

Gevoeligheids labels zijn belang rijk voor het beveiligen van uw bedrijfs kritieke gegevens en u kunt op een compatibele en veilige manier groepen op schaal beheren. Zie voor hulp bij het gebruik van gevoeligheids labels de [labels voor gevoeligheid toewijzen aan Microsoft 365 groepen in azure Active Directory (preview)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Updates voor de ondersteuning van Microsoft Identity Manager voor Azure AD Premium klanten

**Type:** Gewijzigde functie  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Ondersteuning voor Azure is nu beschikbaar voor Azure AD-integratie onderdelen van Microsoft Identity Manager 2016, tot het einde van uitgebreide ondersteuning voor Microsoft Identity Manager 2016. Meer informatie vindt [u in de ondersteunings update voor Azure AD Premium klanten die gebruikmaken van Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Het gebruik van de voor waarden voor groepslid maatschappen in de SSO-claim configuratie is verhoogd

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Voorheen werd het aantal groepen dat u zou kunnen gebruiken wanneer u de claims op basis van het groepslid maatschap in een configuratie van één toepassing voorwaardelijk wijzigt, beperkt tot 10. Het gebruik van de voor waarden voor groepslid maatschappen in de SSO-claim configuratie is nu verhoogd naar Maxi maal 50 groepen. Raadpleeg voor meer informatie over het configureren van claims de configuratie van de [SSO-claim voor bedrijfs toepassingen](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Het inschakelen van basis opmaak op het onderdeel tekst van de aanmeldings pagina in de huis stijl van het bedrijf.

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
De functionaliteit voor het huis stijl van het bedrijf op de Azure AD/Microsoft 365-aanmeld ervaring is bijgewerkt, zodat de klant hyper links en eenvoudige opmaak kan toevoegen, waaronder vet letter type, onderstrepen en cursief. Zie [huisstijl toevoegen aan de aanmeldings pagina Azure Active Directory van uw organisatie](./customize-branding.md)voor meer informatie over het gebruik van deze functie.

---

### <a name="provisioning-performance-improvements"></a>Prestatie verbeteringen inrichten

**Type:** Gewijzigde functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De inrichtings service is bijgewerkt om de tijd te verkorten om een [incrementele cyclus](../app-provisioning/how-provisioning-works.md#incremental-cycles) te volt ooien. Dit betekent dat gebruikers en groepen sneller worden ingericht in hun toepassingen dan voorheen. Alle nieuwe inrichtings taken die zijn gemaakt na 6/10/2020, profiteren automatisch van de prestatie verbeteringen. Alle toepassingen die zijn geconfigureerd voor het inrichten vóór 6/10/2020, moeten na 6/10/2020 opnieuw worden opgestart om te profiteren van de prestatie verbeteringen. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Aankondiging van de afschaffing van ADAL en MS Graph-pariteit

**Type:** Keur  
**Service categorie:** n.v.t.  
**Product mogelijkheden:** Levenscyclus beheer van apparaten

Nu de micro soft-verificatie bibliotheken (MSAL) beschikbaar zijn, worden er geen nieuwe functies meer toegevoegd aan de Azure Active Directory authenticatie bibliotheken (ADAL) en worden er op 30 juni 2022 beveiligings patches afgesloten. Raadpleeg [toepassingen migreren naar micro soft Authentication Library (MSAL)](../develop/msal-migration.md)voor meer informatie over het migreren naar MSAL.

Daarnaast hebben we het werk voltooid om alle functionaliteit van Azure AD Graph beschikbaar te maken via MS Graph. Azure AD Graph-Api's ontvangen daarom alleen bugfix-en beveiligings oplossingen tot en met 30 juni 2022. Zie [uw toepassingen bijwerken voor het gebruik van micro soft-verificatie bibliotheek en Microsoft Graph-API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) voor meer informatie
 
---
## <a name="may-2020"></a>Mei 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Buiten gebruik stellen van eigenschappen in aanmeldingen-, riskyUsers-en riskDetections-Api's

**Type:** Plan voor wijziging  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op dit moment worden opgesomde typen gebruikt om de eigenschap riskType in de riskDetections-API en riskyUserHistoryItem (in Preview) weer te geven. Opgesomde typen worden ook gebruikt voor de eigenschap riskEventTypes in de aanmeldingen-API. Deze eigenschappen worden weer gegeven als teken reeksen. 

Klanten moeten overstappen op de eigenschap riskEventType in de API Beta riskDetections en riskyUserHistoryItem, en naar riskEventTypes_v2 eigenschap in de Beta aanmeldingen API van 9 september 2020. Op die datum wordt de huidige riskType-en riskEventTypes-eigenschappen buiten gebruik gesteld. Zie [wijzigingen in de eigenschappen van de risico gebeurtenis en api's voor identiteits beveiliging op Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)voor meer informatie.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Afschaffing van de eigenschap riskEventTypes in de aanmeldingen v 1.0 API op Microsoft Graph

**Type:** Plan voor wijziging  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Opgesomde typen worden overgeschakeld naar teken reeks typen bij het weer geven van eigenschappen van risico gebeurtenissen in Microsoft Graph september 2020. Deze wijziging is niet alleen van invloed op de preview-Api's en is ook van invloed op de in-Production aanmeldingen-API.

We hebben een nieuwe riskEventsTypes_v2-eigenschap (teken reeks) geïntroduceerd in de API van aanmeldingen v 1.0. De huidige riskEventTypes (enum)-eigenschap op 11 juni 2022 wordt buiten gebruik gesteld volgens ons Microsoft Graph afschaffing-beleid. Klanten moeten op 11 juni 2022 overstappen op de eigenschap riskEventTypes_v2 in de API van aanmeldingen v 1.0. Raadpleeg de [eigenschap riskEventTypes in aanmeldingen v 1.0 API op Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)voor meer informatie.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Aanstaande wijzigingen in MFA-e-mail meldingen

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 

De volgende wijzigingen worden in de e-mail meldingen voor Cloud MFA aangebracht:

E-mail meldingen worden verzonden vanaf het volgende adres: azure-noreply@microsoft.com en msonlineservicesteam@microsoftonline.com . De inhoud van e-mail berichten voor fraude bestrijding worden bijgewerkt om beter te kunnen aangeven wat de vereiste stappen zijn voor het deblokkeren van het gebruik.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nieuwe self-service registratie voor gebruikers in federatieve domeinen die geen toegang hebben tot micro soft-teams omdat ze niet zijn gesynchroniseerd met Azure Active Directory.

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 

Op dit moment kunnen gebruikers die zich in domeinen bevinden in azure AD, maar die niet zijn gesynchroniseerd met de Tenant, geen toegang krijgen tot teams. Vanaf het einde van juni kan deze nieuwe functionaliteit dit doen door de bestaande registratie functie voor geverifieerde e-mail uit te breiden. Hierdoor kunnen gebruikers die zich aanmelden bij een federatieve IdP, maar die nog geen gebruikers object hebben in azure ID, een gebruikers object automatisch maken en worden geverifieerd voor teams. Hun gebruikers object wordt als ' self-service registreren ' gemarkeerd. Dit is een uitbrei ding van de bestaande mogelijkheid om e-mail berichten te laten verifiëren die gebruikers in beheerde domeinen kunnen uitvoeren en kunnen worden beheerd met dezelfde vlag. Deze wijziging wordt tijdens de volgende twee maanden voltooid. Kijk [hier](../enterprise-users/directory-self-service-signup.md)voor documentatie-updates.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Aanstaande oplossing: het detectie document voor OIDC voor de Azure Government Cloud wordt bijgewerkt om te verwijzen naar de juiste grafiek eindpunten.

**Type:** Plan voor wijziging  
**Service categorie:** Soevereine Clouds  
**Product mogelijkheden:** Gebruikers verificatie
 
Vanaf juni zal het OIDC Discovery-document [micro soft Identity platform en OpenID Connect Connect protocol](../develop/v2-protocols-oidc.md) op het [Azure Government Cloud](../develop/authentication-national-cloud.md) -eind punt (login.microsoftonline.us) het juiste nationale eind punt voor de [Cloud grafiek](/graph/deployments) retour neren ( https://graph.microsoft.us of https://dod-graph.microsoft.us) , op basis van de beschik bare Tenant.  Het bevat momenteel het onjuiste grafiek eindpunt (graph.microsoft.com) msgraph_host veld.  

Deze fout oplossing wordt geleidelijk ongeveer 2 maanden uitgerold.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government gebruikers zich niet meer kunnen aanmelden bij login.microsoftonline.com

**Type:** Plan voor wijziging  
**Service categorie:** Soevereine Clouds  
**Product mogelijkheden:** Gebruikers verificatie
 
Op 1 juni 2018 is de officiële Azure Active Directory-instantie (Azure AD) voor Azure Government gewijzigd van https://login-us.microsoftonline.com naar https://login.microsoftonline.us . Als u eigenaar bent van een toepassing binnen een Azure Government-Tenant, moet u uw toepassing bijwerken om gebruikers in te schrijven op het eind punt. VS.

Vanaf 5 mei is Azure AD bezig met het afdwingen van de wijziging van het eind punt, waardoor Azure Government gebruikers zich kunnen aanmelden bij apps die worden gehost in Azure Government tenants met behulp van het open bare eind punt (microsoftonline.com). Met betrokken apps wordt een fout AADSTS900439-USGClientNotSupportedOnPublicEndpoint weer gegeven. 

Er wordt een geleidelijke implementatie van deze wijziging met betrekking tot de afdwinging die naar verwachting is voltooid voor alle apps juni 2020. Zie het [blog bericht Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)voor meer informatie.

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>De aanvraag voor eenmalige SAML-afmelding verzendt nu de juiste indeling voor NameID

**Type:** Vaste  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Wanneer een gebruiker op Afmelden klikt (bijvoorbeeld in de MyApps-Portal), verzendt Azure AD een single-afmeldings bericht via SAML naar elke app die actief is in de gebruikers sessie en dat er een afmeldings-URL is geconfigureerd. Deze berichten bevatten een NameID in een permanente indeling.

Als het oorspronkelijke SAML-aanmeldings token een andere indeling heeft gebruikt voor NameID (bijvoorbeeld e-mail/UPN), kan de SAML-app niet correleren in het afmeldings bericht naar een bestaande sessie (omdat de NameIDs die in beide berichten wordt gebruikt, anders is), waardoor het afmeldings bericht wordt veroorzaakt door de SAML-app en de gebruiker aangemeld blijft. Met deze oplossing wordt het afmeldings bericht consistent met het NameID dat is geconfigureerd voor de toepassing.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>De rol hybride identiteits beheerder is nu beschikbaar met Cloud inrichting

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-Cloud inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
IT-beheerders kunnen beginnen met het gebruik van de nieuwe rol ' hybride beheerder ' als de rol van de minste bevoegdheden voor het instellen van Azure AD Connect Cloud inrichting. Met deze nieuwe rol hoeft u niet langer de rol van globale beheerder te gebruiken voor het instellen en configureren van Cloud inrichting. [Meer informatie](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-mei 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In mei 2020 hebben we de volgende 36 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC record Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-ingeschakeld](https://b-engaged.se/), [Competella contact centrum werk groep](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft identiteit](https://identity.imagesoftinc.com/), [mijn IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [wijzigings proces beheer](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [intelligent Global governance](../saas-apps/smart-global-governance-tutorial.md), [PREZI](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enter prise service platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO voor confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO voor BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO voor bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii,](../saas-apps/torii-tutorial.md) [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [humanisatie](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander,](../saas-apps/textexpander-tutorial.md) [iedereen Home CRM](../saas-apps/anyone-home-crm-tutorial.md) [, askSpoke,](../saas-apps/askspoke-tutorial.md) [Ice contact centrum](../saas-apps/ice-contact-center-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial .

Lees de informatie in de app-galerie van Azure AD voor het weer geven van uw toepassing https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

[Met de modus alleen rapport voor voorwaardelijke toegang van Azure AD](../conditional-access/concept-conditional-access-report-only.md) kunt u het resultaat van een beleid evalueren zonder dat er toegangs beheer wordt afgedwongen. U kunt alleen beleids regels voor rapporten in uw organisatie testen en hun impact op hun eigen effect controleren, zodat de implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie van de modus alleen rapport weer gegeven, maar 26M gebruikers zijn al in het bereik van een beleid voor alleen een rapport. Met de aankondiging van vandaag worden nieuwe beleids regels voor voorwaardelijke toegang van Azure AD standaard in de modus alleen rapport gemaakt. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die de MS Graph-Api's gebruiken, kunt u ook op [een programmatische manier beleids regels voor rapporten beheren](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Self-service registratie voor gast gebruikers

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
Met externe identiteiten in azure AD kunt u personen buiten uw organisatie toegang verlenen tot uw apps en resources, terwijl ze zich aanmelden met behulp van de identiteit van hun voor keur. Wanneer u een toepassing deelt met externe gebruikers, weet u mogelijk niet altijd vooraf wie toegang tot de toepassing nodig heeft. Met de [self-service registratie](../external-identities/self-service-sign-up-overview.md)kunt u gast gebruikers in staat stellen zich aan te melden en een gast account te verkrijgen voor uw LOB-apps (line-of-Business). De registratie stroom kan worden gemaakt en aangepast ter ondersteuning van Azure AD en sociale identiteiten. U kunt ook aanvullende informatie over de gebruiker verzamelen tijdens het aanmelden.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapporten over voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De [werkmap inzichten en rapporten](../conditional-access/howto-conditional-access-insights-reporting.md) biedt beheerders een samen vatting van de voorwaardelijke toegang van Azure AD in hun Tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten [na deze instructies](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)kunt instellen. Om het dash board beter detecteerbaar te maken, is het naar het tabblad nieuw inzicht en rapportage in het menu voorwaardelijke toegang van Azure AD verplaatst.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>De Blade beleids Details voor voorwaardelijke toegang is beschikbaar als open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op de [Blade nieuwe beleids Details](../conditional-access/troubleshoot-conditional-access.md) worden de toewijzingen, voor waarden en besturings elementen weer gegeven die zijn voldaan tijdens de evaluatie van het voorwaardelijke toegangs beleid. U kunt de Blade openen door een rij te selecteren op de tabbladen voorwaardelijke toegang of alleen rapport met alleen rapporten van de aanmeldings gegevens.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nieuwe query mogelijkheden voor Active Directory-objecten in Microsoft Graph zijn beschikbaar in de open bare preview

**Type:** Nieuwe functie  
**Service categorie:** MS Graph- **product mogelijkheid:** ervaring voor ontwikkel aars

Er worden nieuwe mogelijkheden geïntroduceerd voor Microsoft Graph Directory-object-Api's, het inschakelen van Count-, Zoek-, filter-en sorteer bewerkingen. Dit biedt ontwikkel aars de mogelijkheid om snel een query uit te brengen op onze Directory-objecten zonder tijdelijke oplossingen zoals in-Memory filtering en sorteren. Meer informatie vindt u in dit [blog bericht](https://aka.ms/CountFilterMSGraphAAD).

We zijn momenteel beschikbaar als open bare preview-versie en zoeken naar feedback. Stuur uw opmerkingen met deze [korte enquête](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Eenmalige aanmelding op basis van SAML configureren met behulp van Microsoft Graph-API (bèta)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Ondersteuning voor het maken en configureren van een toepassing vanuit de Azure AD-galerie met behulp van MS Graph-Api's in Beta is nu beschikbaar. Als u eenmalige aanmelding op basis van SAML moet instellen voor meerdere exemplaren van een toepassing, kunt u tijd besparen door de Microsoft Graph-Api's te gebruiken om [de configuratie van eenmalige aanmelding op basis van SAML te automatiseren](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-mei 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure-console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML-token versleuteling is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
Met [SAML-token versleuteling](../manage-apps/howto-saml-token-encryption.md) kunnen toepassingen worden geconfigureerd voor het ontvangen van versleutelde SAML-bevestigingen. De functie is nu algemeen beschikbaar in alle Clouds.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Groeps naam claims in toepassings tokens is algemeen beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO
 
De groeps claims die in een token zijn uitgegeven, kunnen nu worden beperkt tot alleen de groepen die zijn toegewezen aan de toepassing.  Dit is vooral belang rijk wanneer gebruikers lid zijn van een groot aantal groepen en er sprake is van een risico dat de limieten voor de token grootte worden overschreden. Als deze nieuwe mogelijkheid is ingesteld, is de mogelijkheid om [groeps namen aan tokens toe te voegen](../hybrid/how-to-connect-fed-group-claims.md) algemeen beschikbaar.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Voor de werkdag wordt nu ondersteuning geboden voor het instellen van werk telefoonnummer kenmerken

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
We hebben de app voor het terugschrijven van werk dagen uitgebreid om nu write-back van telefoon nummer en mobiele nummer kenmerken te ondersteunen. Naast e-mail en gebruikers naam kunt u nu de werk stroom voor het terugschrijven van werk dagen configureren voor het door sturen van telefoon nummer waarden van Azure AD naar workday. Raadpleeg voor meer informatie over het configureren van telefoon nummer terugschrijven de hand leiding voor het [terugschrijven van workday](../saas-apps/workday-writeback-tutorial.md) -apps. 

---

### <a name="publisher-verification-preview"></a>Uitgever verificatie (preview-versie)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
De verificatie van de uitgever (preview) helpt beheerders en eind gebruikers inzicht te krijgen in de echtheid van toepassings ontwikkelaars die integreren met het micro soft Identity-platform. Raadpleeg de [Publisher-verificatie (preview)](../develop/publisher-verification-overview.md)voor meer informatie.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Autorisatie code stroom voor apps met één pagina

**Type:** Gewijzigde functie **service categorie:** verificatie **product mogelijkheid:** ontwikkelaars ervaring

Vanwege de moderne [Cookie beperkingen van derden, zoals Safari ITP](../develop/reference-third-party-cookies-spas.md), moet Spas de autorisatie code stroom gebruiken in plaats van de impliciete stroom om SSO te onderhouden. MSAL.js v 2. x ondersteunt nu de autorisatie code stroom. Er zijn gerelateerde updates voor de Azure Portal zodat u uw beveiligd-wachtwoord verificatie kunt bijwerken met het type ' Spa ' en de auth-code stroom moet gebruiken. Raadpleeg voor meer informatie [Quick Start: gebruikers aanmelden en een toegangs token verkrijgen in een Java script Spa met behulp van de verificatie code stroom](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Verbeterde filtering voor apparaten is beschikbaar in de open bare preview

**Type:** Gewijzigde functie   
**Service categorie:** **Product functionaliteit** voor Apparaatbeheer: levenscyclus beheer van apparaten
 
Voorheen werden de enige filters die u zou kunnen gebruiken ' ingeschakeld ' en ' activiteit datum '. Nu kunt u [uw lijst met apparaten filteren op meer eigenschappen](../devices/device-management-azure-portal.md#device-list-filtering-preview), waaronder type besturings systeem, type samen voeging, naleving en meer. Deze toevoegingen moeten het zoeken naar een bepaald apparaat vereenvoudigen.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>De nieuwe App-registraties-ervaring voor Azure AD B2C is nu algemeen verkrijgbaar

**Type:** Gewijzigde functie   
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De nieuwe App-registraties-ervaring voor Azure AD B2C is nu algemeen beschikbaar. 

Voorheen moest u uw B2C-consumenten toepassingen onafhankelijk van de rest van uw apps beheren met behulp van de oudere toepassingen. Dat heeft verschillende ervaring voor het maken van apps op verschillende locaties in Azure.

De nieuwe ervaring toont alle B2C en Azure AD-App-registraties op één locatie en biedt een consistente manier om deze te beheren. Of u nu een klant gerichte app of een app wilt beheren die toegang heeft tot Microsoft Graph om Azure AD B2C resources programmatisch te beheren, maar u hoeft slechts één manier te leren om dingen te doen.

U kunt de nieuwe ervaring bereiken door te navigeren door de Azure AD B2C-service en de App-registraties-Blade te selecteren. De ervaring is ook toegankelijk vanuit de Azure Active Directory-service.

De Azure AD B2C App-registraties-ervaring is gebaseerd op de [ervaring](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) voor de registratie van algemene apps voor Azure AD-tenants, maar is afgestemd op Azure AD B2C. De verouderde-ervaring voor toepassingen wordt in de toekomst afgeschaft.

Ga naar [de nieuwe app registratie-ervaring voor Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md)voor meer informatie.

---
## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>De registratie-ervaring voor gecombineerde beveiligings gegevens is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De gecombineerde registratie-ervaring voor Multi-Factor Authentication (MFA) en Self-Service wacht woord opnieuw instellen (SSPR) is nu algemeen beschikbaar. Met deze nieuwe registratie-ervaring kunnen gebruikers zich registreren voor MFA en SSPR in één stap-voor-stap proces. Wanneer u de nieuwe ervaring voor uw organisatie implementeert, kunnen gebruikers zich minder tijd en minder moeite registreren. Bekijk [hier](https://bit.ly/3etiRyQ)het blog bericht.

---

### <a name="continuous-access-evaluation"></a>Evaluatie van voortdurende toegang

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Evaluatie van doorlopende toegang is een nieuwe beveiligings functie die bijna in realtime beleids handhaving mogelijk maakt voor relying party's die Azure AD-toegangs tokens gebruiken wanneer er gebeurtenissen optreden in azure AD (zoals het verwijderen van een gebruikers account). Deze functie wordt eerst geïmplementeerd voor teams en Outlook-clients. Lees onze [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) en de  [documentatie](../conditional-access/concept-continuous-access-evaluation.md)voor meer informatie.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-aanmelding: Firstline-werk nemers kunnen zich aanmelden bij toepassingen met Azure AD-back-ups met hun telefoon nummer en zonder wacht woord

**Type:** Nieuwe functie

**Service categorie:** Authenticaties (aanmeldingen)

**Product mogelijkheden:** Gebruikers verificatie

Office start een reeks mobiele, zakelijke apps die zijn vrijmaken voor niet-traditionele organisaties en aan werk nemers in grote organisaties die geen e-mail gebruiken als hun primaire communicatie methode. Deze apps richten zich op Frontline werk nemers, Deskless-werk nemers, veld agenten of werk nemers van de winkel, die mogelijk geen e-mail adres van hun werk gever ontvangen, toegang hebben tot een computer of naar de andere. Met dit project kunnen deze werk nemers zich aanmelden bij zakelijke toepassingen door een telefoon nummer en roundtripping een code in te voeren. Raadpleeg voor meer informatie onze [beheer documentatie](../authentication/howto-authentication-sms-signin.md) en documentatie voor [eind gebruikers](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Interne gebruikers uitnodigen voor het gebruik van B2B-samen werking

**Type:** Nieuwe functie

**Service categorie:** Business

**Product mogelijkheden:**

De functie voor B2B-uitnodiging wordt uitgebreid zodat bestaande interne accounts kunnen worden uitgenodigd om B2B-samenwerkings referenties vooruit te gebruiken. Dit doet u door het gebruikers object door te geven aan de API voor uitnodigen naast de gebruikelijke para meters zoals het e-mail adres dat u hebt uitgenodigd. De object-ID van de gebruiker, de UPN, het groepslid maatschap, de app-toewijzing, enzovoort blijven intact, maar ze worden doorgestuurd met B2B voor verificatie met hun Tenant referenties in plaats van de interne referenties die ze vóór de uitnodiging hebben gebruikt. Raadpleeg de [documentatie](../external-identities/invite-internal-users.md)voor meer informatie.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

[Met de modus alleen rapport voor voorwaardelijke toegang van Azure AD](../conditional-access/concept-conditional-access-report-only.md) kunt u het resultaat van een beleid evalueren zonder dat er toegangs beheer wordt afgedwongen. U kunt alleen beleids regels voor rapporten in uw organisatie testen en hun impact op hun eigen effect controleren, zodat de implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie van de modus alleen rapport gezien, met meer dan 26M gebruikers die reeds het bereik van een alleen-rapport beleid hebben. Met deze aankondiging wordt standaard het beleid voor voorwaardelijke toegang van Azure AD in de modus alleen rapport gemaakt. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die de MS Graph Api's gebruiken, kunt u ook beleid voor [alleen een rapport beheren via een programma](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapporten over voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

De Insights- [werkmap](../conditional-access/howto-conditional-access-insights-reporting.md) voor voorwaardelijke toegang biedt beheerders een samen vatting van de voorwaardelijke toegang van Azure AD in hun Tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten [na deze instructies](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)kunt instellen. Om het dash board beter detecteerbaar te maken, is het naar het tabblad nieuw inzicht en rapportage in het menu voorwaardelijke toegang van Azure AD verplaatst.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>De Blade beleids Details voor voorwaardelijke toegang is beschikbaar als open bare preview

**Type:** Nieuwe functie

**Service categorie:** Voorwaardelijke toegang

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Op de [Blade nieuwe beleids Details](../conditional-access/troubleshoot-conditional-access.md) worden de toewijzingen, voor waarden en besturings elementen weer gegeven die zijn voldaan tijdens de evaluatie van het voorwaardelijke toegangs beleid. U kunt de Blade openen door een rij te selecteren op de tabbladen **voorwaardelijke toegang** of **alleen rapport met alleen rapporten** van de aanmeldings gegevens.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-april 2020

**Type:** Nieuwe functie

**Service categorie:** Zakelijke apps

**Product capaciteit:** integratie van derden

In april 2020 hebben we deze 31 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[SincroPool apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT inkoop Suite](../saas-apps/iwt-procurement-suite-tutorial.md) [Lunni](https://lunni.fi/), [EasySSO voor Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki dash board](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365](https://app.mover.io/login)-overschakeling, [spreker](https://speakerengage.com/login.php), [honestly](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 mensen](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonie](../saas-apps/harmony-tutorial.md), [Timetabling oplossingen](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet Klik](../saas-apps/synchronet-click-tutorial.md), [Empower](https://www.made-in-office.com/en/), [veertig](../saas-apps/fortes-change-cloud-tutorial.md) [, litmus,](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDb Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS leren](../saas-apps/tickitlms-learn-tutorial.md), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro productiviteits pakket](../saas-apps/nitro-productivity-suite-tutorial.md), [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor oAuth2PermissionGrant beschikbaar voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor oAuth2PermissionGrant is beschikbaar voor de open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph ondersteuning voor Delta query's voor contact personen in de organisatie algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor contact personen in de organisatie is algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu orgContact-gegevens doorzoekt door de Delta query om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph Delta-query ondersteuning voor de toepassing algemeen beschikbaar

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

De Delta query voor toepassingen is over het algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. Vervang bestaande code die continu toepassings gegevens doorzoekt op Delta query's om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph Delta-query ondersteuning voor beheerders eenheden die beschikbaar zijn voor open bare preview

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** De Delta-query voor ontwikkelaars ervaring voor beheer eenheden is beschikbaar voor open bare preview. U kunt nu wijzigingen bijhouden zonder voortdurend te hoeven pollen Microsoft Graph. [Meer informatie.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Telefoon nummers voor verificatie en meer beheren in nieuwe Microsoft Graph bèta-Api's

**Type:** Nieuwe functie

**Service categorie:** MS Graph

**Product mogelijkheden:** Ontwikkelaars ervaring

Deze Api's zijn een belang rijk hulp programma voor het beheren van de verificatie methoden van uw gebruikers. Nu kunt u de verificators die worden gebruikt voor MFA en self-service voor wachtwoord herstel (SSPR), vooraf registreren en beheren. Dit is een van de meest aangevraagde functies in azure AD MFA, SSPR en Microsoft Graph Spaces. De nieuwe Api's die we in deze Wave hebben uitgebracht, bieden u de volgende mogelijkheden:

- De verificatie-telefoons van een gebruiker lezen, toevoegen, bijwerken en verwijderen
- Het wacht woord van een gebruiker opnieuw instellen
- SMS-aanmelden in-of uitschakelen

Zie [Azure AD-verificatie methoden API overview](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)(Engelstalig) voor meer informatie.

---

### <a name="administrative-units-public-preview"></a>Open bare preview van administratieve eenheden

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Met beheereenheden kunt u beheerdersmachtigingen verlenen die zijn beperkt tot een afdeling, een regio of een ander door u gedefinieerd segment van uw organisatie. U kunt beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om een beleid op een gedetailleerd niveau in te stellen. Beheerders van gebruikersaccounts kunnen bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor uitsluitend gebruikers die in hun beheereenheid zijn opgenomen.

Met beheer eenheden kan een centrale beheerder:

- Een administratieve eenheid maken voor gedecentraliseerd beheer van resources
- Een rol met beheerders machtigingen alleen toewijzen via Azure AD-gebruikers in een beheer eenheid
- Vul de beheer eenheden met gebruikers en groepen in als dat nodig is

Zie beheer [eenheden beheren in azure Active Directory (preview)](../roles/administrative-units.md)voor meer informatie.

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ingebouwde rollen van de printer beheerder en de printer technicus

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

**Printer beheerder**: gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printer configuraties in de micro soft-oplossing voor universele afdrukken beheren, met inbegrip van de instellingen voor de universele afdruk connector. Ze kunnen toestemming geven voor alle gedelegeerde afdruk machtigings aanvragen. Printer beheerders hebben ook toegang tot het afdrukken van rapporten. 

**Printer technicus**: gebruikers met deze rol kunnen printers registreren en printer status beheren in de micro soft Universal Print-oplossing. Ze kunnen ook alle connector gegevens lezen. Belangrijkste taken een printer technicus kan geen gebruikers machtigingen voor printers instellen en printers delen. [Meer informatie.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ingebouwde rol voor hybride identiteits beheerder

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in azure AD inschakelen, configureren en beheren. Deze rol biedt de mogelijkheid om Azure AD te configureren voor een van de drie ondersteunde verificatie methoden&#8212;wacht woord-hash synchronisatie (PHS), Pass-Through-verificatie (PTA) of Federatie (AD FS of externe Federatie provider) &#8212;en een gerelateerde on-premises infra structuur te implementeren om deze in te scha kelen. On-premises infra structuur omvat het inrichten en PTA agents. Deze rol biedt de mogelijkheid om naadloze single Sign-On (S-SSO) in te scha kelen om naadloze verificatie mogelijk te maken op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Daarnaast biedt deze rol de mogelijkheid om aanmeldings logboeken te bekijken en de status en analyse te openen voor het bewaken en oplossen van problemen. [Meer informatie.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ingebouwde rol netwerk beheerder

**Type:** Nieuwe functie

**Service categorie:** Azure AD-rollen

**Product mogelijkheden:** Access Control

Gebruikers met deze rol kunnen aanbevelingen van de netwerk architectuur beoordelen van micro soft die zijn gebaseerd op telemetrie van het netwerk vanaf hun gebruikers locaties. De netwerk prestaties voor Microsoft 365 zijn gebaseerd op een zorgvuldige netwerk verbindings architectuur van de Enter prise-klant. Dit is doorgaans een gebruikersspecifieke locatie. Deze rol maakt het mogelijk om de gedetecteerde gebruikers locaties en configuratie van de netwerk parameters voor die locaties te bewerken, zodat de telemetriegegevens en ontwerp aanbevelingen kunnen worden verbeterd. [Meer informatie.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Bulk activiteit en down loads in de Azure AD-beheer Portal

**Type:** Nieuwe functie

**Service categorie:** Gebruikers beheer

**Product mogelijkheden:** Uitvoermap

U kunt nu bulk activiteiten uitvoeren op gebruikers en groepen in azure AD door een CSV-bestand te uploaden in de Azure AD-beheer Portal. U kunt gebruikers maken, gebruikers verwijderen en gast gebruikers uitnodigen. En u kunt leden toevoegen aan en verwijderen uit een groep.

U kunt ook lijsten van Azure AD-resources downloaden vanuit de Azure AD-beheer Portal. U kunt de lijst met gebruikers in de Directory, de lijst met groepen in de Directory en de leden van een bepaalde groep downloaden.

Voor meer informatie raadpleegt u het volgende:

- [Gebruikers maken](../enterprise-users/users-bulk-add.md) of [gast gebruikers uitnodigen](../external-identities/tutorial-bulk-invite.md)
- [Gebruikers verwijderen](../enterprise-users/users-bulk-delete.md) of [Verwijderde gebruikers herstellen](../enterprise-users/users-bulk-restore.md)
- [Lijst met gebruikers downloaden](../enterprise-users/users-bulk-download.md) of [lijst met groepen downloaden](../enterprise-users/groups-bulk-download.md)
- [Leden toevoegen (importeren)](../enterprise-users/groups-bulk-import-members.md) of [leden verwijderen](../enterprise-users/groups-bulk-remove-members.md) of de [lijst met leden](../enterprise-users/groups-bulk-download-members.md) voor een groep downloaden

---

### <a name="my-staff-delegated-user-management"></a>Mijn personeel heeft gebruikers beheer gedelegeerd

**Type:** Nieuwe functie

**Service categorie:** Gebruikers beheer

**Product mogelijkheden:**

Mijn personeel stelt Firstline-managers, zoals een Store Manager, in staat om ervoor te zorgen dat hun mede werkers toegang hebben tot hun Azure AD-accounts. In plaats van te vertrouwen op een Central-Help Desk kunnen organisaties algemene taken, zoals het opnieuw instellen van wacht woorden of het wijzigen van telefoon nummers, overdragen aan een Firstline Manager. Met mijn mede werkers kunnen gebruikers die geen toegang hebben tot hun account, in slechts enkele klikken toegang krijgen, zonder dat er een helpdesk medewerker of IT-personeel nodig is. Zie voor meer informatie het [beheren van uw gebruikers met mijn personeel (preview)](../roles/my-staff-configure.md) en het [delegeren van gebruikers beheer met mijn personeel (preview)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Een bijgewerkte ervaring voor eind gebruikers in toegangs beoordelingen

**Type:** Gewijzigde functie

**Service categorie:** Toegangs beoordelingen

**Product mogelijkheden:** Identity governance

We hebben de revisor ervaring voor Azure AD-toegangs beoordelingen bijgewerkt in de portal mijn apps. Aan het einde van april zien uw revisoren die zijn aangemeld bij de Azure AD-toegangs beoordelingen, een banner dat hen in staat stelt om de bijgewerkte ervaring in mijn toegang te proberen. Houd er rekening mee dat de bijgewerkte ervaring voor toegangs beoordelingen dezelfde functionaliteit biedt als de huidige ervaring, maar met een verbeterde gebruikers interface bovenop nieuwe functies zodat uw gebruikers productief kunnen zijn. [Hier vindt u meer informatie over de bijgewerkte ervaring](../governance/perform-access-review.md). Deze open bare preview gaat tot eind juli 2020. Aan het einde van juli worden revisoren die zich niet bij de preview-ervaring hebben aangemeld, automatisch omgeleid naar mijn toegang om toegangs beoordelingen uit te voeren. Als u wilt dat uw revisoren de preview-versie in mijn Access nu blijven gebruiken, kunt u [hier een aanvraag](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)indienen.

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Werkdag inkomende gebruikers inrichten en write-back-apps ondersteunen nu de nieuwste versies van de workday Web Services-API

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** 

Op basis van feedback van klanten hebben we nu de werk dagen inkomend gebruikers inrichten en write-back-apps bijgewerkt in de galerie met zakelijke apps om de nieuwste versies van de WWS-API (workday Web Services) te ondersteunen. Met deze wijziging kunnen klanten de WWS API-versie opgeven die ze willen gebruiken in de connection string. Dit biedt klanten de mogelijkheid om meer HR-kenmerken op te halen die beschikbaar zijn in de releases van workday. In de workday-app voor het terugschrijven wordt nu gebruikgemaakt van de aanbevolen Change_Work_Contact_Info workday-webservice om de beperkingen van Maintain_Contact_Info te overwinnen.

Als er in de connection string geen versie is opgegeven, blijven de inkomende inrichtings-apps van de werkdag WWS v 21.1 gebruiken om over te scha kelen naar de laatste workday-Api's voor inkomend gebruikers inrichten, moeten klanten de connection string bijwerken zoals beschreven [in de zelf studie](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) en ook de XPath-waarden bijwerken die worden gebruikt voor workday-kenmerken zoals beschreven in de [Naslag Gids voor kenmerk dagen](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Als u de nieuwe API voor terugschrijven wilt gebruiken, zijn er geen wijzigingen vereist in de app write-inrichtings toepassing. Zorg ervoor dat op de dag van de workday het ISU-account (voor de werkdag Integration System) machtigingen heeft voor het aanroepen van het Change_Work_Contact bedrijfs proces zoals beschreven in de sectie zelf studie, de machtigingen voor het [beveiligings beleid voor het bedrijfs proces configureren](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

We hebben onze [hand leiding voor de zelf studie](../saas-apps/workday-inbound-tutorial.md) bijgewerkt met de nieuwe API-versie ondersteuning.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gebruikers met een standaard functie voor toegang zijn nu het bereik voor het inrichten

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Gebruikers met de standaard-Access-rol hebben in het verleden geen bereik voor het inrichten. We hebben feedback gehoord die klanten willen dat gebruikers met deze rol binnen het bereik van het inrichten moeten zijn. Vanaf 16 april 2020 kunnen gebruikers met alle nieuwe inrichtings configuraties de standaard-Access-rol inrichten. Geleidelijk zullen we het gedrag voor bestaande inrichtings configuraties wijzigen om het inrichten van gebruikers met deze rol te ondersteunen. [Meer informatie.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>De gebruikers interface voor inrichting is bijgewerkt

**Type:** Gewijzigde functie

**Service categorie:** App-inrichting

**Product mogelijkheden:** Beheer van identiteits levenscyclus

Onze inrichtings ervaring is vernieuwd om een beter gerichtere beheer weergave te maken. Wanneer u navigeert naar de Blade inrichten voor een bedrijfs toepassing die al is geconfigureerd, kunt u de voortgang van het inrichten en beheren van acties op eenvoudige wijze bewaken, zoals het starten, stoppen en opnieuw starten van het inrichten. [Meer informatie.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validatie van dynamische groeps regel is nu beschikbaar voor open bare preview

**Type:** Gewijzigde functie

**Service categorie:** Groeps beheer

**Product mogelijkheden:** Werking

Azure Active Directory (Azure AD) biedt nu de mogelijkheid om dynamische groeps regels te valideren. Op het tabblad **regels valideren** kunt u uw dynamische regel valideren op basis van voor beelden van groeps leden om te bevestigen dat de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groeps regels willen beheerders weten of een gebruiker of een apparaat lid is van de groep. Dit helpt u te evalueren of een gebruiker of apparaat voldoet aan de regel criteria en hulp bij het oplossen van problemen wanneer het lidmaatschap niet wordt verwacht.

Zie [een dynamische regel voor groepslid maatschap valideren (preview)](../enterprise-users/groups-dynamic-rule-validation.md)voor meer informatie.

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identiteits veilige Score-beveiligings standaards en updates voor de actie voor MFA-verbetering

**Type:** Gewijzigde functie

**Service categorie:** n.v.t.

**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

**Ondersteuning voor de standaard instellingen voor de verbetering van de beveiliging van Azure AD:** Met micro soft Secure Score worden verbeteringen voor de verbetering bijgewerkt zodat de [standaard instellingen van de beveiliging in azure AD](./concept-fundamentals-security-defaults.md)worden ondersteund, waardoor het eenvoudiger wordt om uw organisatie te beschermen met vooraf geconfigureerde beveiligings instellingen voor veelvoorkomende aanvallen. Dit is van invloed op de volgende verbeterings acties:

- Zorg ervoor dat alle gebruikers multi-factor Authentication voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen
- Beleid inschakelen voor het blok keren van verouderde verificatie
 
**Updates voor de actie voor MFA-verbetering:** Voor de nood zaak van bedrijven om te zorgen voor de beste beveiliging bij het Toep assen van beleid dat samenwerkt met hun bedrijf, heeft micro soft Secure Score drie verbeteringen voor verbetering verwijderd die zijn gecentreerd rond multi-factor Authentication en twee worden toegevoegd.

Verbeterings acties verwijderd:

- Alle gebruikers registreren voor multi-factor Authentication
- MFA vereisen voor alle gebruikers
- MFA vereisen voor Azure AD-privileged roles

Toegevoegde acties voor verbetering:

- Zorg ervoor dat alle gebruikers multi-factor Authentication voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen

Voor deze nieuwe maat regelen voor verbetering moet u uw gebruikers of beheerders registreren voor multi-factor Authentication (MFA) in uw directory en de juiste set beleids regels instellen die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is om de flexibiliteit te bieden en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of prompts voor identiteits verificatie op basis van een risico. Dit kan de vorm hebben van meerdere beleids regels waarmee beslissingen met een bereik worden toegepast, of het instellen van standaard instellingen voor beveiliging (vanaf 16 maart) waarmee micro soft beslist wanneer gebruikers voor MFA moeten worden aangevraagd. [Lees meer over wat er nieuw is in micro soft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Maart 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Onbeheerde Azure Active Directory accounts in B2B-update voor maart 2021

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
**Vanaf 31 maart 2021** heeft micro soft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure Active Directory (Azure AD)-accounts en tenants voor B2B-samenwerkings scenario's. Ter voor bereiding van dit voor deel raden we u aan om te kiezen voor [verificatie met eenmalige wachtwoord code](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gebruikers met de standaard-Access-rol zullen binnen het bereik van de inrichting vallen

**Type:** Plan voor wijziging  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Gebruikers met de standaard-Access-rol hebben in het verleden geen bereik voor het inrichten. We hebben feedback gehoord die klanten willen dat gebruikers met deze rol binnen het bereik van het inrichten moeten zijn. Er wordt gewerkt aan het implementeren van een wijziging zodat gebruikers met de standaard-Access-rol kunnen worden ingericht. Geleidelijk zullen we het gedrag voor bestaande inrichtings configuraties wijzigen om het inrichten van gebruikers met deze rol te ondersteunen. Er is geen actie van de klant vereist. Zodra deze wijziging is doorgevoerd, zullen we een update naar onze [documentatie](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) posten.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samen werking is beschikbaar in Microsoft Azure beheerd door 21Vianet-tenants (Azure China 21Vianet)

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De samenwerkings mogelijkheden van Azure AD B2B worden beschikbaar gesteld in Microsoft Azure die worden beheerd door 21Vianet-tenants (Azure China 21Vianet), waardoor gebruikers in een Azure China 21Vianet-Tenant naadloos kunnen samen werken met gebruikers in andere Azure China 21Vianet-tenants. Meer [informatie over Azure AD B2B-samen werking](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration-uitnodiging opnieuw ontwerpen

**Type:** Plan voor wijziging  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De [e-mail berichten](../external-identities/invitation-email-elements.md) die door de uitnodiging van de Azure AD B2B-samen werking worden verzonden om gebruikers uit te nodigen voor de Directory, worden opnieuw ontworpen om de informatie over de uitnodiging en de volgende stappen van de gebruiker duidelijker te maken.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Wijzigingen in het HomeRealmDiscovery-beleid worden weer gegeven in de audit logboeken

**Type:** Vaste  
**Service categorie:** Controle  
**Product mogelijkheden:** & rapportage controleren
 
Er is een fout opgelost waarbij wijzigingen in het [HomeRealmDiscovery-beleid](../manage-apps/configure-authentication-for-federated-users-portal.md) niet zijn opgenomen in de audit Logboeken. U kunt nu zien wanneer en hoe het beleid is gewijzigd en door wie. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-maart 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In 2020 maart hebben we deze 51 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML app](../saas-apps/profitco-saml-app-tutorial.md), [IPoint service provider](../saas-apps/ipoint-service-provider-tutorial.md), [contexxt.ai bol](https://contexxt-sphere.com/login), [expertise verkregen by Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [flare Digital Signing](https://spark-dev.pixelnebula.com/login), [Logz.io-Cloud waarneembaar voor technici](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano-SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge learning suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [netvision kompas](../saas-apps/netvision-compas-tutorial.md), [FCM hub](../saas-apps/fcm-hub-tutorial.md), [rib A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](../saas-apps/golinks-tutorial.md), [Datadog](../saas-apps/datadog-tutorial.md), Zscaler [B2B gebruikers Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [Lift](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md) [, WatchTeams,](https://www.devfinition.com/) [Aster](https://demo.asterapp.io/login), [vaardigheden werk stroom](../saas-apps/skills-workflow-tutorial.md), [knooppunt inzicht](https://admin.nodeinsight.com/AADLogin.aspx), [IP-platform](../saas-apps/ip-platform-tutorial.md), [Invision](../saas-apps/invision-tutorial.md), [pipe drive](../saas-apps/pipedrive-tutorial.md), [show case](https://app.showcaseworkshop.com/), [GreenLight Integration platform](../saas-apps/greenlight-integration-platform-tutorial.md), [GreenLight compliant toegangs beheer](../saas-apps/greenlight-compliant-access-management-tutorial.md), [grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [, bizagi](../saas-apps/britive-tutorial.md) [Studio voor Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuitex](https://www.insuite.jp/) [, sybo, Britive](https://www.systexsoftware.com.tw/), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-dagen](../saas-apps/e-days-tutorial.md), [Kollective Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn-veer boot 360](../saas-apps/korn-ferry-360-tutorial.md), [campus café](../saas-apps/campus-cafe-tutorial.md), [Catchpoint](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samen werking beschikbaar in Azure Government tenants

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** B2B/B2C
 
De functies van Azure AD B2B-samen werking zijn nu beschikbaar tussen enkele Azure Government-tenants.  Als u wilt weten of uw Tenant deze mogelijkheden kan gebruiken, volgt u de instructies op [Hoe kan ik zien of B2B-samen werking beschikbaar is in mijn Azure US Government-Tenant?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integratie voor Azure-Logboeken is nu beschikbaar in Azure Government

**Type:** Nieuwe functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Azure Monitor integratie met Azure AD-Logboeken is nu beschikbaar in Azure Government. U kunt Azure AD-Logboeken (audit-en aanmeld Logboeken) door sturen naar een opslag account, Event hub en Log Analytics. Raadpleeg de [gedetailleerde documentatie](../reports-monitoring/concept-activity-logs-azure-monitor.md) [en implementatie plannen voor rapportage en bewaking](../reports-monitoring/plan-monitoring-and-reporting.md) voor Azure AD-scenario's.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Vernieuwen van identiteits beveiliging in Azure Government

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

We zijn enthousiast over het delen van de vernieuwde [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)   ervaring in de [Microsoft Azure Government Portal](https://portal.azure.us/). Zie voor meer informatie ons [aankondigings blog bericht](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Herstel na nood geval: uw inrichtings configuratie downloaden en opslaan

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service biedt een uitgebreide set configuratie mogelijkheden. Klanten moeten hun configuratie kunnen opslaan zodat ze deze later kunnen raadplegen of terugdraaien naar een bekende goede versie. We hebben de mogelijkheid toegevoegd om uw inrichtings configuratie te downloaden als een JSON-bestand en deze te uploaden wanneer u deze nodig hebt. [Meer informatie](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self-service voor wacht woord opnieuw instellen) vereist nu twee poorten voor beheerders in Microsoft Azure die worden beheerd door 21Vianet (Azure China 21Vianet) 

**Type:** Gewijzigde functie  
**Service categorie:** Self-Service wacht woord opnieuw instellen  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Voorheen in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet), beheerders die gebruikmaken van selfservice voor wachtwoord herstel (SSPR) om hun eigen wacht woorden opnieuw in te stellen, was er slechts één ' Gate ' (uitdaging) nodig om hun identiteit te bewijzen. In open bare en andere nationale Clouds moeten beheerders doorgaans twee poorten gebruiken om hun identiteit te bewijzen wanneer ze SSPR gebruiken. Maar omdat we geen ondersteuning bieden voor SMS-of telefoon gesprekken in azure China 21Vianet, hebben we een wacht woord voor het opnieuw instellen van één poort per beheerder toegestaan.

Er wordt een SSPR-functie pariteit gemaakt tussen Azure China 21Vianet en de open bare Cloud. Beheerders moeten twee poorten gebruiken bij gebruik van SSPR. SMS-, telefoon oproepen en verificatie van de verificator-app worden ondersteund. [Meer informatie](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>De wachtwoord lengte is beperkt tot 256 tekens

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Om ervoor te zorgen dat de Azure AD-service betrouwbaar is, zijn gebruikers wachtwoorden nu beperkt tot 256 tekens. Gebruikers met een wacht woord dat langer is dan dit wordt gevraagd om hun wacht woorden te wijzigen bij de volgende aanmelding, door contact op te nemen met de beheerder of door gebruik te maken van de selfservice functie voor het opnieuw instellen van een wacht woord.

Deze wijziging is ingeschakeld op 13 maart, 2020, op 10AM PST (18:00 UTC) en de fout is AADSTS 50052, InvalidPasswordExceedsMaxLength. Raadpleeg de [melding](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) over het breken voor meer informatie.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Aanmeldings logboeken voor Azure AD zijn nu beschikbaar voor alle gratis tenants via de Azure Portal

**Type:** Gewijzigde functie  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Vanaf nu kunnen klanten met gratis tenants toegang krijgen tot de [aanmeldings logboeken van Azure AD van de Azure Portal](../reports-monitoring/concept-sign-ins.md) gedurende 7 dagen. Voorheen werden aanmeldings Logboeken alleen beschikbaar voor klanten met Azure Active Directory Premium-licenties. Met deze wijziging kunnen alle tenants toegang krijgen tot deze logboeken via de portal.

> [!NOTE]
> Klanten hebben nog een Premium-licentie (Azure Active Directory Premium P1 of P2) nodig om toegang te krijgen tot de aanmeld logboeken via Microsoft Graph API en Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Uitschakeling van de optie voor de hele Directory groepen uit de algemene instellingen voor groepen op Azure Portal

**Type:** Keur  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

Om ervoor te zorgen dat klanten flexibele groepen kunnen maken die het beste aan hun behoeften voldoen, hebben we de optie voor de **hele Directory groepen** vervangen uit de   >  **algemene** instellingen voor groepen in de Azure Portal met een koppeling naar de [documentatie van dynamische groepen](../enterprise-users/groups-dynamic-membership.md). We hebben onze documentatie verbeterd met meer instructies zodat beheerders alle groepen kunnen maken die gast gebruikers bevatten of uitsluiten.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Aanstaande wijzigingen in aangepaste besturings elementen

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
We zijn van plan de huidige aangepaste controle voorbeelden te vervangen door een benadering waarmee de verificatie mogelijkheden van een partner naadloos kunnen samen werken met de Azure Active Directory beheerder en de ervaring van de eind gebruiker. Tegenwoordig hebben partner MFA-oplossingen de volgende beperkingen: ze werken alleen nadat er een wacht woord is ingevoerd. ze fungeren niet als MFA voor het opschalen van verificatie in andere belang rijke scenario's. en ze zijn niet geïntegreerd met beheer functies voor eind gebruikers of beheer referenties. De nieuwe implementatie zorgt ervoor dat door de partner meegeleverde verificatie factoren naast ingebouwde factoren worden gebruikt voor belang rijke scenario's, waaronder registratie, gebruik, MFA-claims, verificatie, rapportage en logboek registratie. 

Aangepaste besturings elementen worden nog steeds ondersteund in Preview naast het nieuwe ontwerp, totdat de algemene Beschik baarheid wordt bereikt. Op dat moment geven we klanten tijd om te migreren naar het nieuwe ontwerp. Vanwege de beperkingen van de huidige aanpak kunnen we geen nieuwe providers vrijgeven totdat het nieuwe ontwerp beschikbaar is. We werken nauw samen met klanten en providers en zullen de tijd lijn door geven als we dichter bij elkaar komen. [Meer informatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identiteits beveiligde Score

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Voor de nood zaak van bedrijven om te zorgen voor de beste beveiliging bij het Toep assen van beleid dat met hun bedrijf werkt, wordt door micro soft Secure Score drie verbeteringen voor verbetering van de multi-factor Authentication (MFA) verwijderd en worden er twee toegevoegd.

De volgende verbeterings acties worden verwijderd:

- Alle gebruikers voor MFA registreren
- MFA vereisen voor alle gebruikers
- MFA vereisen voor Azure AD-privileged roles

De volgende verbeterings acties worden toegevoegd:

- Zorg ervoor dat alle gebruikers MFA voor beveiligde toegang kunnen volt ooien
- MFA vereisen voor beheerders rollen

Voor deze nieuwe verbeterings acties moet u uw gebruikers of beheerders registreren voor MFA in uw directory en de juiste set beleids regels instellen die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is om de flexibiliteit te bieden en ervoor te zorgen dat al uw gebruikers en beheerders kunnen verifiëren met meerdere factoren of prompts voor identiteits verificatie op basis van een risico. Dit kan de vorm hebben van de standaard instellingen voor beveiliging waarmee micro soft kan bepalen wanneer gebruikers voor MFA moeten worden aangevraagd of wanneer er meerdere beleids regels gelden voor het Toep assen van beslissingen met een bereik. Als onderdeel van deze verbeteringen van de verbeterings actie, worden beleids regels voor basis beveiliging niet meer opgenomen in Score berekeningen. [Meer informatie over de micro soft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU selecteren

**Type:** Nieuwe functie  
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services
 
We hebben feedback gehoord die Azure AD Domain Services klanten meer flexibiliteit willen bij het selecteren van prestatie niveaus voor hun instanties. Vanaf 1 februari 2020 wordt er overgeschakeld van een dynamisch model (waarbij Azure AD de prestaties en prijs categorie op basis van het aantal objecten bepaalt) in een zelf-selectie model. Klanten kunnen nu een prestatie niveau kiezen dat overeenkomt met hun omgeving. Met deze wijziging kunnen we ook nieuwe scenario's als resource-forests inschakelen, en Premium-functies, zoals dagelijkse back-ups. Het aantal objecten is nu onbeperkt voor alle Sku's, maar we blijven suggesties voor object aantal voor elke laag aanbieden.

**Er is geen onmiddellijke klant actie vereist.** Voor bestaande klanten bepaalt de dynamische laag die werd gebruikt op 1 februari 2020, de nieuwe standaardlaag. Er zijn geen prijzen of gevolgen voor de prestaties als gevolg van deze wijziging. Azure AD DS klanten moeten de prestatie vereisten evalueren als de grootte van de Directory en de kenmerken van de werk belasting worden gewijzigd. Scha kelen tussen service lagen blijft een niet-downtimede bewerking en worden klanten niet meer automatisch verplaatst naar nieuwe lagen op basis van de groei van hun Directory. Bovendien zijn er geen prijs verhogingen en worden nieuwe prijzen afgestemd op het huidige facturerings model. Zie de [documentatie van Azure AD DS sku's](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) en de [Azure AD Domain Services-pagina met prijzen](https://azure.microsoft.com/pricing/details/active-directory-ds/)voor meer informatie.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-februari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In februari 2020 zijn deze 31 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[IamIP patent platform](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [ns1 SSO voor Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda e-mail beveiligings service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [in het geval van een crisis-Online Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [BIC-Cloud ontwerp](../saas-apps/bic-cloud-design-tutorial.md), [bijen teelt Azure AD-gegevens connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [Korn-veer](https://www.kornferry.com/solutions/kf-digital/kf-assess)tests, [Verkada-opdracht](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB navigatie](../saas-apps/eab-navigate-tutorial.md), [nieuwe Relic (beperkte release)](../saas-apps/new-relic-limited-release-tutorial.md), [thulium](https://admin.thulium.com/login/instance), [ticket Manager](../saas-apps/ticketmanager-tutorial.md), [sjabloon kiezer voor teams](https://links.officeatwork.com/templatechooser-download-teams), [bijen](https://www.beesy.me/index.php/site/login), [Health-ondersteunings systeem](../saas-apps/health-support-system-tutorial.md), [Mural](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex vdr](../saas-apps/firmex-vdr-tutorial.md), ThingLink [voor docenten en scholen](https://www.thinglink.com/), [CODA](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [Bureau-artikel Galaxy en afdruk](../saas-apps/reprints-desk-article-galaxy-tutorial.md) [team](../saas-apps/teamviewer-tutorial.md)

 
Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-februari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud van Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-ondersteuning voor FIDO2-beveiligings sleutels in hybride omgevingen

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
We kondigen de open bare preview-versie van Azure AD-ondersteuning voor FIDO2-beveiligings sleutels in hybride omgevingen. Gebruikers kunnen nu FIDO2-beveiligings sleutels gebruiken om zich aan te melden bij hun hybride Azure AD-apparaten die lid zijn van Windows 10 en een naadloze aanmelding voor hun on-premises en Cloud bronnen te krijgen. Ondersteuning voor hybride omgevingen is de meest aangevraagde functie van onze klanten met een wacht woord, omdat we in eerste instantie de open bare Preview voor FIDO2-ondersteuning in azure AD gekoppelde apparaten hebben gestart. Verificatie zonder wacht woord met behulp van geavanceerde technologieën zoals biometrie en crypto grafie met een open bare/persoonlijke sleutel kunt u gemakkelijk en eenvoudig gebruik maken van de beveiliging. Met deze open bare preview kunt u nu moderne verificatie gebruiken, zoals FIDO2-beveiligings sleutels, om toegang te krijgen tot traditionele Active Directory resources. Ga naar [SSO voor on-premises resources](../authentication/howto-authentication-passwordless-security-key-on-premises.md)voor meer informatie. 

Om aan de slag te gaan, gaat u naar [FIDO2-beveiligings sleutels voor uw Tenant inschakelen](../authentication/howto-authentication-passwordless-security-key.md) voor stapsgewijze instructies. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>De nieuwe ervaring mijn account is nu algemeen verkrijgbaar

**Type:** Gewijzigde functie  
**Service categorie:** Mijn profiel/account  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Mijn account, de ene-stop-shop voor alle beheer behoeften voor eind gebruikers, is nu algemeen beschikbaar. Eind gebruikers hebben toegang tot deze nieuwe site via URL of in de koptekst van de nieuwe mijn apps-ervaring. Meer informatie over de self-service mogelijkheden van de nieuwe ervaring vindt u op [Mijn account Portal overzicht](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Site-URL van mijn account wordt bijgewerkt naar myaccount.microsoft.com

**Type:** Gewijzigde functie  
**Service categorie:** Mijn profiel/account  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
De nieuwe ervaring voor eind gebruikers van mijn account wordt `https://myaccount.microsoft.com` in de volgende maand bijgewerkt naar de URL. Meer informatie over de ervaring en alle accounts selfservice mogelijkheden voor eind gebruikers vindt u in de [Help van mijn account Portal](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>De nieuwe portal voor mijn apps is nu algemeen verkrijgbaar

**Type:** Plan voor wijziging  
**Service categorie:** Mijn apps  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Voer een upgrade uit van uw organisatie naar de nieuwe portals voor mijn apps die nu algemeen beschikbaar zijn. U vindt meer informatie over de nieuwe portal en verzamelingen in [verzamelingen maken in de portal mijn apps](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>De naam van werk ruimten in azure AD is gewijzigd in verzamelingen

**Type:** Gewijzigde functie  
**Service categorie:** Mijn apps   
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Werk ruimten, de filters beheerders kunnen configureren om de apps van hun gebruikers te organiseren, worden nu verzamelingen genoemd. Meer informatie over hoe u deze kunt configureren in [verzamelingen maken in de portal mijn apps](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefoon registratie en aanmelding met aangepast beleid (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Met het registreren van telefoon nummers en aanmelding kunnen ontwikkel aars en ondernemingen hun klanten toestaan zich aan te melden en zich aan te melden met een eenmalig wacht woord dat via SMS naar het telefoon nummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook hun telefoon nummer wijzigen als de toegang tot hun telefoon verloren is gegaan. Met de kracht van aangepaste beleids regels en aanmelding via de telefoon kunnen ontwikkel aars en ondernemingen hun merk via de pagina aanpassen. Meer informatie over het [instellen van aanmelding via de telefoon en het aanmelden met aangepaste beleids regels in azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-januari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-januari 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In januari 2020 hebben we deze 33 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie: 

[JOSA](../saas-apps/josa-tutorial.md), [snelle Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [TERRAFORM Enter prise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [](../saas-apps/upshotly-tutorial.md)upsneld, [LEAVEBOT](https://appsource.microsoft.com/en-us/product/office/WA200001175), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SmartWork](https://www.intumit.com/english/SmartWork.html), [DOTCOM-monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN-Azure AD SSO-gateway voor Oracle E-Business Suite-EBS, People Soft en JDE](../saas-apps/ssogen-tutorial.md), [gehoste MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), [Upwerk onderneming](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB voor micro soft teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), ContractSafe [Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient uitvoeren Manager-software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), squelch [Cloud Office365-connector](https://laxmi.squelch.io/login), [PingFlow-verificatie](https://app-staging.pingview.io/), [PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), Akari [virtuele assistent](https://akari.io/akari-virtual-assistant/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="two-new-identity-protection-detections"></a>Twee nieuwe detecties van identiteits beveiliging

**Type:** Nieuwe functie  
**Service categorie:** Identiteits beveiliging  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Er zijn twee nieuwe gekoppelde aanmeld typen toegevoegd aan identiteits beveiliging: verdacht regels voor het bewerken van het postvak in en mogelijk geen reizen. Deze offline detecties worden gedetecteerd door Microsoft Cloud App Security (MCAS) en beïnvloeden de gebruikers-en aanmeldings Risico's voor identiteits beveiliging. Zie voor meer informatie over deze detecties de [typen aanmeldings Risico's](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Laatste wijziging: URI-fragmenten worden niet door de omleiding van de aanmelding uitgevoerd

**Type:** Gewijzigde functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie
 
Vanaf 8 februari 2020 wordt een leeg fragment aan de aanvraag toegevoegd wanneer een aanvraag wordt verzonden naar login.microsoftonline.com om zich aan te melden bij een gebruiker.  Dit voor komt een klasse van omleidings aanvallen door ervoor te zorgen dat de browser alle bestaande fragmenten in de aanvraag wist. Een toepassing moet afhankelijk zijn van dit gedrag. Zie belang rijke [wijzigingen](../develop/reference-breaking-changes.md#february-2020) in de micro soft Identity platform-documentatie voor meer informatie.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors-inrichting integreren in azure AD en on-premises AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

U kunt SAP SuccessFactors nu integreren als een gezaghebbende identiteits bron in azure AD. Deze integratie helpt u bij het automatiseren van de end-to-end identiteits levenscyclus, met inbegrip van HR-gebeurtenissen, zoals nieuwe mede werkers of beëindigingen, voor het beheren van de inrichting van Azure AD-accounts.

Zie de zelf studie [SAP SuccessFactors Automatic Provisioning configureren](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) voor meer informatie over het instellen van SAP SuccessFactors inkomend inrichten voor Azure AD.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Ondersteuning voor aangepaste e-mail berichten in Azure AD B2C (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C

U kunt nu Azure AD B2C gebruiken om aangepaste e-mail berichten te maken wanneer uw gebruikers zich aanmelden om uw apps te gebruiken. Met behulp van DisplayControls (momenteel in Preview) en een e-mail provider van derden (zoals, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)of een aangepaste rest API), kunt u uw eigen e-mail sjabloon gebruiken, **van** adres en onderwerps tekst, maar ook lokalisatie en aangepaste otp-instellingen (eenmalig wacht woord) ondersteunen.

Zie [aangepaste e-mail verificatie in azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md)voor meer informatie.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Vervanging van basislijn beleid met standaard instellingen voor beveiliging

**Type:** Gewijzigde functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Identiteits beveiliging en-beveiliging

Als onderdeel van een beveiligd model voor verificatie, verwijderen we de bestaande Baseline-beveiligings beleidsregels van alle tenants. Deze verwijdering is bedoeld om aan het einde van februari te worden voltooid. De vervanging voor dit basis beveiligings beleid is de standaard instellingen voor beveiliging. Als u Baseline-beveiligings beleidsregels hebt gebruikt, moet u plannen om over te scha kelen naar het nieuwe beleid voor beveiligings standaarden of naar voorwaardelijke toegang. Als u dit beleid nog niet hebt gebruikt, hoeft u geen actie te ondernemen.

Zie wat zijn de standaard beveiligings [instellingen?](./concept-fundamentals-security-defaults.md) voor meer informatie over de nieuwe standaard waarden voor de beveiliging. Zie [common Conditional Access policies](../conditional-access/concept-conditional-access-policy-common.md)(Engelstalig) voor meer informatie over beleids regels voor voorwaardelijke toegang.

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** Plan voor wijziging  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Gebruikers verificatie

Als onderdeel van een beveiligd standaard model voor cookies wordt de Chrome 80-browser gewijzigd hoe cookies zonder het kenmerk worden behandeld `SameSite` . Elke cookie waarbij het kenmerk niet `SameSite` wordt opgegeven, wordt behandeld alsof het is ingesteld op `SameSite=Lax` . Hierdoor kunnen Chrome bepaalde scenario's voor het delen van cookies op meerdere domeinen blok keren waarvan uw app afhankelijk is. Als u het oudere Chrome-gedrag wilt behouden, kunt u het `SameSite=None` kenmerk gebruiken en een extra `Secure` kenmerk toevoegen, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Chrome is gepland om deze wijziging te volt ooien op 4 februari 2020.

We raden aan dat alle ontwikkel aars hun apps testen met behulp van deze richt lijnen:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- Stel de standaard waarde voor het kenmerk **SameSite** in op **geen**.

- Voeg een extra `SameSite` kenmerk van **Secure** toe.

Zie voor meer informatie [aanstaande SameSite cookie wijzigingen in ASP.net en ASP.net core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) en [mogelijke onderbreking van klant websites en micro soft-producten en-services in Chrome versie 79 en hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nieuwe hotfix voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** Vaste  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Er is een hotfixcombinatiepakket (build 4.6.34.0) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Met dit pakket Rollup worden problemen opgelost en worden verbeteringen toegevoegd die worden beschreven in de sectie ' problemen opgelost en verbeteringen toegevoegd in deze update '.

Voor meer informatie over het downloaden van het hotfix-pakket raadpleegt u het [Update pakket voor Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) beschikbaar](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nieuw AD FS app-activiteiten rapport voor het migreren van apps naar Azure AD (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

Gebruik het nieuwe Active Directory Federation Services (AD FS) app-activiteiten rapport in de Azure Portal om te bepalen welke van uw apps kunnen worden gemigreerd naar Azure AD. In het rapport worden alle AD FS-apps beoordeeld op compatibiliteit met Azure AD, wordt er gecontroleerd of er problemen zijn en vindt u hulp bij het voorbereiden van afzonderlijke apps voor migratie.

Zie [het rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD](../manage-apps/migrate-adfs-application-activity.md)voor meer informatie.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nieuwe werk stroom voor gebruikers om toestemming van de beheerder aan te vragen (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** Access Control

De nieuwe beheerder van de beheerders toestemming biedt beheerders een manier om toegang te verlenen tot apps waarvoor beheerder goed keuring is vereist. Als een gebruiker toegang probeert te krijgen tot een app, maar geen toestemming kan geven, kan deze nu een aanvraag indienen voor goed keuring door de beheerder. De aanvraag wordt per e-mail verzonden en in een wachtrij geplaatst die toegankelijk is vanaf de Azure Portal, naar alle beheerders die zijn aangewezen als controleurs. Nadat een revisor actie ondervindt voor een aanvraag in behandeling, worden de aanvragen van de gebruiker op de hoogte gesteld van de actie.

Zie [de beheerder toestemming werk stroom configureren (preview)](../manage-apps/configure-admin-consent-workflow.md)voor meer informatie.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nieuwe Azure AD-app registraties de token configuratie-ervaring voor het beheren van optionele claims (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Ontwikkelaars ervaring

Op de Blade nieuw **Azure AD-App registraties voor token configuratie** op de Azure Portal worden nu app-ontwikkel aars een dynamische lijst met optionele claims voor hun apps weer gegeven. Deze nieuwe ervaring helpt bij het stroom lijnen van Azure AD-App-migraties en voor het minimaliseren van de configuratie van de optionele claims.

Zie [optionele claims voor uw Azure AD-app bieden](../develop/active-directory-optional-claims.md)voor meer informatie.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nieuwe goedkeurings werk stroom voor twee fases in azure AD-rechts beheer (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten

We hebben een nieuwe goedkeurings werk stroom in twee fasen geïntroduceerd waarmee u twee goed keurders in staat stelt om de aanvraag van een gebruiker goed te keuren voor een toegangs pakket. U kunt dit bijvoorbeeld instellen zodat de Manager van de gebruiker die de aanvraag heeft ingediend eerst moet goed keuren, en u kunt ook een resource-eigenaar verplichten om goed te keuren. Als een van de goed keurders niet goed keuren, wordt geen toegang verleend.

Zie [instellingen voor aanvraag en goed keuring wijzigen voor een toegangs pakket in azure AD-rechts beheer](../governance/entitlement-management-access-package-request-policy.md)voor meer informatie.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates voor de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** Nieuwe functie  
**Service categorie:** Mijn apps  
**Product capaciteit:** integratie van derden

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de vernieuwde mijn apps-ervaring weer geven en openen. Deze nieuwe ervaring omvat ook de functie nieuwe werk ruimten, waarmee uw gebruikers gemakkelijker apps kunnen vinden en indelen.

Zie [werk ruimten maken in de portal mijn apps](../manage-apps/access-panel-collections.md)voor meer informatie over de nieuwe ervaring voor het gebruik van mijn apps en het maken van werk ruimten.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Ondersteuning voor Google-sociale ID voor Azure AD B2B-samen werking (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Business  
**Product mogelijkheden:** Gebruikers verificatie

Nieuwe ondersteuning voor het gebruik van Google-sociale Id's (Gmail-accounts) in azure AD helpt om samen werking eenvoudiger te maken voor uw gebruikers en partners. Het is niet meer nodig om uw partners om een nieuw micro soft-specifiek account te maken en te beheren. Micro soft teams biedt nu volledige ondersteuning voor Google-gebruikers op alle clients en in de gemeen schappelijke en Tenant-gerelateerde verificatie-eind punten.

Zie [Google als id-provider voor B2B-gast gebruikers toevoegen](../external-identities/google-federation.md)voor meer informatie.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Mobiele ondersteuning van micro soft Edge voor voorwaardelijke toegang en eenmalige aanmelding (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &

Azure AD voor micro soft Edge op iOS en Android ondersteunt nu Azure AD single Sign-On en voorwaardelijke toegang:

- Eenmalige **aanmelding voor micro soft Edge (SSO):** Eenmalige aanmelding is nu beschikbaar via systeem eigen clients (zoals micro soft Outlook en micro soft Edge) voor alle apps die zijn verbonden met Azure AD.

- **Voorwaardelijke toegang voor micro soft Edge:** Met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen moeten uw gebruikers gebruikmaken van Microsoft Intune beveiligde browsers, zoals micro soft Edge.

Voor meer informatie over voorwaardelijke toegang en eenmalige aanmelding met micro soft Edge raadpleegt u het blog bericht [mobiele ondersteuning voor voorwaardelijke toegang en eenmalige aanmelding die nu algemeen beschikbaar is](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Voor meer informatie over het instellen van uw client-apps met behulp [van voorwaardelijke toegang op basis van apps](../conditional-access/app-based-conditional-access.md) of [voorwaardelijke toegang op basis](../conditional-access/require-managed-devices.md)van een apparaat, raadpleegt [u Web access beheren met een met Microsoft intune-beleid beveiligde browser](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Beheer van rechten van Azure AD (algemene Beschik baarheid)

**Type:** Nieuwe functie  
**Service categorie:** Daarenteg  
**Product mogelijkheden:** Beheer rechten

Het beheer van rechten van Azure AD is een nieuwe functie voor Identity governance, waarmee organisaties de levens cyclus van identiteiten en toegang op schaal kunnen beheren. Deze nieuwe functie helpt u bij het automatiseren van werk stromen voor toegangs aanvragen, toegangs toewijzingen, beoordelingen en verloop tijd voor groepen, apps en share point online-sites.

Met het beheer van rechten van Azure AD kunt u de toegang efficiënter beheren voor werk nemers en voor gebruikers buiten uw organisatie die toegang nodig hebben tot deze resources.

Zie [Wat is Azure AD-rechts beheer?](../governance/entitlement-management-overview.md#license-requirements) voor meer informatie.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden  

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

[SAP Cloud platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace software](../saas-apps/officespace-software-provisioning-tutorial.md), [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-november 2019

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In november 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[HootSuite](../saas-apps/hootsuite-tutorial.md) [, de](../saas-apps/airtable-tutorial.md) [blauwe toegang voor leden (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal single sign on (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [FOKO Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets idee & innovatie beheer](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope gebruikers verificatie](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [jisc student gevoel registratie](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nieuwe en verbeterde Azure AD-toepassings galerie

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben de Azure AD-toepassings galerie bijgewerkt zodat u gemakkelijk vooraf geïntegreerde apps kunt vinden die ondersteuning bieden voor inrichting, OpenID Connect Connect en SAML op uw Azure Active Directory-Tenant.

Zie [een toepassing toevoegen aan uw Azure Active Directory-Tenant](../manage-apps/add-application-portal.md)voor meer informatie.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>De lengte limiet van de functie definitie van de app-rol is verhoogd van 120 tot 240 tekens

**Type:** Gewijzigde functie  
**Service categorie:** Zakelijke apps  
**Product mogelijkheden:** SSO

We hebben van klanten gehoord dat de lengte limiet voor de definitie waarde van de app-functie in sommige apps en services te kort is om 120 tekens. Als antwoord hebben we de maximale lengte van de roldefinitie-waarde verhoogd tot 240 tekens.

Zie [app-functies toevoegen in uw toepassing en ontvangen in het token](../develop/howto-add-app-roles-in-azure-ad-apps.md)voor meer informatie over het gebruik van toepassingsspecifieke roldefinities.

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Afschaffing van de identityRiskEvent-API voor detectie van Azure AD Identity Protection-Risico's

**Type:** Planning voor wijziging **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Als reactie op de feedback van ontwikkel aars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op Azure AD Identity Protection risico detectie gegevens door gebruik te maken van de nieuwe riskDetection-API voor Microsoft Graph. De bestaande [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API bèta versie retourneert geen gegevens over **10 januari 2020**. Als uw organisatie gebruikmaakt van de identityRiskEvent-API, moet u overstappen naar de nieuwe riskDetection-API.

Zie de [referentie documentatie voor API voor risico detectie](/graph/api/resources/riskdetection)voor meer informatie over de nieuwe RISKDETECTION-API.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor toepassings proxy voor het kenmerk SameSite en Chrome 80

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Een paar weken voorafgaand aan de Chrome 80-browser release, willen we bijwerken hoe cookies van toepassings proxy het kenmerk **SameSite** behandelen. Met de release van Chrome 80 wordt elke cookie waarbij het kenmerk **SameSite** niet wordt opgegeven, behandeld alsof het is ingesteld op `SameSite=Lax` .

Om te voor komen dat mogelijk negatieve gevolgen zijn door deze wijziging, worden de toegangs-en sessie cookies van de toepassings proxy bijgewerkt door:

- Stel de standaard waarde voor de instelling **beveiligde cookie gebruiken** in op **Ja**.

- De standaard waarde voor het kenmerk **SameSite** in te stellen op **geen**.

    >[!NOTE]
    > Application proxy-toegangs cookies zijn altijd uitsluitend via beveiligde kanalen verzonden. Deze wijzigingen zijn alleen van toepassing op sessie cookies.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md)voor meer informatie over de cookie-instellingen van de toepassings proxy.

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>App-registraties (verouderd) en app-beheer in het portal voor toepassings registratie (apps.dev.microsoft.com) is niet meer beschikbaar

**Type:** Planning voor **service categorie wijzigen:** n.v.t. **product mogelijkheid:** ontwikkelaars ervaring

Gebruikers met Azure AD-accounts kunnen geen toepassingen meer registreren of beheren via de portal voor toepassings registratie (apps.dev.microsoft.com) of toepassingen registreren en beheren in de App-registraties (verouderde) ervaring in de Azure Portal.

Raadpleeg voor meer informatie over de nieuwe App-registraties-ervaring de [app-registraties in de hand leiding voor Azure Portal training](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers hoeven zich niet langer opnieuw te registreren tijdens de migratie van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

**Type:** Vaste **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Er is een bekend probleem opgelost waarbij gebruikers zich opnieuw moeten registreren als ze zijn uitgeschakeld voor per gebruiker-Multi-Factor Authentication (MFA) en vervolgens via een beleid voor voorwaardelijke toegang zijn ingeschakeld voor MFA.

Als u wilt dat gebruikers zich opnieuw moeten registreren, kunt u de optie voor het **opnieuw registreren van MFA** selecteren uit de verificatie methoden van de gebruiker in de Azure AD-Portal. Voor meer informatie over het migreren van gebruikers van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang, Zie [gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden voor het transformeren en verzenden van claims in uw SAML-token

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We hebben extra mogelijkheden toegevoegd waarmee u claims kunt aanpassen en verzenden in uw SAML-token. Deze nieuwe mogelijkheden zijn onder andere:

- Extra functies voor het transformeren van claims, waarmee u de waarde die u in de claim verzendt, kunt wijzigen.

- De mogelijkheid om meerdere trans formaties op één claim toe te passen.

- De mogelijkheid om de claim bron op te geven op basis van het gebruikers type en de groep waartoe de gebruiker behoort.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie over deze nieuwe mogelijkheden, inclusief hoe u deze kunt gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Pagina nieuwe mijn aanmeldingen voor eind gebruikers in azure AD

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** bewaking & rapportage

We hebben een nieuwe pagina **mijn aanmeldingen** toegevoegd ( https://mysignins.microsoft.com) zodat de gebruikers van uw organisatie hun recente aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Met deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert hun wacht woord te raden.

- Als een aanvaller zich heeft aangemeld bij hun account en vanaf welke locatie.

- Welke apps de aanvaller probeerde te openen.

Zie voor meer informatie de [gebruikers kunnen nu hun aanmeldings geschiedenis controleren op blog van ongebruikelijke activiteiten](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van het klassieke naar Azure Resource Manager virtuele netwerken

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Voor onze klanten die zijn vastgelopen op klassieke virtuele netwerken, hebben we een geweldig nieuws voor u. U kunt nu een eenmalige migratie uitvoeren van een klassiek virtueel netwerk naar een bestaand virtueel netwerk van Resource Manager. Nadat u bent overgestapt op het virtuele netwerk van Resource Manager, kunt u profiteren van de aanvullende en bijgewerkte functies, zoals een verfijnd wachtwoord beleid, e-mail meldingen en audit Logboeken.

Zie [Preview-Azure AD Domain Services migreren van het klassieke virtuele netwerk naar Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md)voor meer informatie.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de indeling van het Azure AD B2C-pagina contract

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

We hebben een aantal nieuwe wijzigingen geïntroduceerd in versie 1.2.0 van het pagina contract voor Azure AD B2C. In deze bijgewerkte versie kunt u nu de laad volgorde voor uw elementen beheren. deze kan ook helpen bij het stoppen van de Flik kering die optreedt wanneer het opmaak model (CSS) wordt geladen.

Raadpleeg het [logboek voor versie](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)wijzigingen voor een volledige lijst van de wijzigingen die in het pagina-contract zijn aangebracht.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Bijwerken naar de pagina mijn apps samen met nieuwe werk ruimten (open bare preview)

**Type:** Nieuwe functie **service categorie:** mijn apps **Product mogelijkheid:** Access Control

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de gloed nieuwe mijn apps-ervaring weer geven en openen, met inbegrip van het gebruik van de nieuwe functie werk ruimten, om het voor hen gemakkelijker te maken apps te vinden. De nieuwe functionaliteit voor werk ruimten fungeert als een filter voor de apps waartoe gebruikers van de organisatie al toegang hebben.

Zie [werk ruimten maken in de portal mijn apps (preview-versie)](../manage-apps/access-panel-collections.md)voor meer informatie over het implementeren van de nieuwe mijn apps-ervaring en het maken van werk ruimten.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijks actieve facturerings model op basis van gebruikers (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

Azure AD B2C ondersteunt nu de facturering van maandelijkse actieve gebruikers (MAU). MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatie activiteiten tijdens een kalender maand. Bestaande klanten kunnen op elk gewenst moment overstappen op deze nieuwe facturerings methode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd met behulp van deze methode. Deze facturerings methode biedt klanten de voor delen van kosten en de mogelijkheid om vooruit te plannen.

Zie voor meer informatie [upgraden naar het facturerings model maandelijkse actieve gebruikers](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-oktober 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In oktober 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[In het geval van crisis: Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno traject](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [intact](https://www.tact.ai/products/tact-assistant), [OpusCapita Liquid Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [contented](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europa](https://www.hirevue.com/), [HireVue-coördinaat-USOnly](https://www.hirevue.com/), [HIREVUE-coördinaten-US](https://www.hirevue.com/), [WittyParrot kennis Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [mail slag!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [snelheid voor teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigeer IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [spreek mail voor intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [gesp roken e-mail voor Office 365 direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care-navigatie systeem](https://ihealthnav.com/account/signin)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item voor geconsolideerde beveiliging in de Azure AD-Portal

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

U hebt nu toegang tot alle beschik bare Azure AD-beveiligings functies vanuit het menu met nieuwe **beveiliging** en op de **Zoek** balk in de Azure Portal. Daarnaast biedt de nieuwe **beveiligings** landings pagina, met de naam **beveiliging-aan de slag**, koppelingen naar onze open bare documentatie, beveiligings richtlijnen en implementatie handleidingen.

Het menu nieuwe **beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Beveiligingscentrum
- Identiteits veilige Score
- Verificatiemethoden
- MFA
- Risico rapporten-Risk ante gebruikers, Risk ante aanmeldingen, risico detecties
- En nog veel meer...

Zie [beveiliging-aan de slag](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)voor meer informatie.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365-groepen verloop beleid verbeterd met autoverlengen

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product mogelijkheden:** Identity Lifecycle Management

Het verloop beleid van de Office 365-groepen is uitgebreid met het automatisch vernieuwen van groepen die momenteel door de leden van de groep worden gebruikt. Groepen worden automatisch vernieuwd op basis van gebruikers activiteit in alle Office 365-apps, waaronder Outlook, share point en teams.

Deze verbetering helpt u bij het verminderen van uw groeps meldingen en helpt ervoor te zorgen dat actieve groepen nog steeds beschikbaar zijn. Als u al een actief verloop beleid hebt voor uw Office 365-groepen, hoeft u niets te doen om deze nieuwe functionaliteit in te scha kelen.

Zie [het verloop beleid voor Office 365-groepen configureren](../enterprise-users/groups-lifecycle.md)voor meer informatie.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Werk ervaring bij het maken van Azure AD Domain Services (Azure AD DS) is bijgewerkt

**Type:** Gewijzigde functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

We hebben Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde ervaring voor het maken van een beheerd domein in slechts drie muis klikken. Daarnaast kunt u nu Azure-AD DS uploaden en implementeren vanuit een sjabloon.

Zie [zelf studie: een Azure Active Directory Domain Services-exemplaar maken en configureren](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie.

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plannen voor wijziging: afschaffing van de Power BI inhouds pakketten

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** bewaking & rapportage

Vanaf 1 oktober 2019 worden alle inhouds pakketten, inclusief het Azure AD Power BI-inhouds pakket, door Power BI af te nemen. Als alternatief voor dit inhouds pakket kunt u Azure AD-werkmappen gebruiken om inzicht te krijgen in uw Azure AD-gerelateerde services. Er zijn extra werkmappen beschikbaar, waaronder werkmappen over beleids regels voor voorwaardelijke toegang in de modus alleen rapport, inzichten op basis van apps en meer.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](../reports-monitoring/howto-use-azure-monitor-workbooks.md)voor meer informatie over de werkmappen. Voor meer informatie over de afschaffing van de inhouds pakketten raadpleegt u het blog bericht [Power bi sjabloon apps voor algemene Beschik baarheid aankondigen](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mijn profiel is een nieuwe naam en integratie met de pagina Microsoft Office-account

**Type:** Planning voor wijziging **service categorie:** mijn profiel/account **product mogelijkheid:** samen werking

Vanaf oktober wordt de ervaring mijn profiel van mijn account. Als onderdeel van deze wijziging, wordt **Mijn profiel** gewijzigd in **Mijn account**. Naast de naam wijziging en enkele ontwerp verbeteringen, biedt de bijgewerkte ervaring aanvullende integratie met de pagina Microsoft Office-account. U hebt met name toegang tot Office-installaties en-abonnementen via de pagina **overzichts account** , samen met Office-gerelateerde contact voorkeuren van de pagina **Privacy** .

Zie voor meer informatie over de ervaring mijn profiel (preview) [Mijn profiel (preview-versie) van portal](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Groepen en leden bulksgewijs beheren met CSV-bestanden in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de ervaring voor bulksgewijs groeps beheer in de Azure AD-Portal. U kunt nu een CSV-bestand en de Azure AD-Portal gebruiken om groepen en leden lijsten te beheren, met inbegrip van:

- Leden toevoegen aan of verwijderen uit een groep.

- De lijst met groepen wordt gedownload uit de map.

- De lijst met groeps leden voor een specifieke groep downloaden.

Zie voor meer informatie [bulksgewijs toevoegen leden](../enterprise-users/groups-bulk-import-members.md), [leden bulksgewijs verwijderen](../enterprise-users/groups-bulk-remove-members.md), [lijst met leden en items](../enterprise-users/groups-bulk-download-members.md)bulksgewijs downloaden en [groepen](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamische toestemming wordt nu ondersteund via een nieuw door de beheerder toestemmings eindpunt

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

We hebben een nieuw beheerders toestemmings eindpunt gemaakt ter ondersteuning van dynamische toestemming. Dit is handig voor apps waarvoor het dynamische toestemming model op het micro soft Identity-platform moet worden gebruikt.

Zie [using the Administrator toestemming endpoint](../develop/v2-admin-consent.md)(Engelstalig) voor meer informatie over het gebruik van dit nieuwe eind punt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-september 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In september 2019 hebben we deze 29 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access voor Ethidex-naleving &trade; Office-eenmalige aanmelding](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [concur Traveling and lasten](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` , [Arc facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [Wide ideeën](https://wideideas.online/wideideas/), [prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT client hub](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath Secure browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/` , `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [gehoste erfgoed online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [bis](../saas-apps/bis-tutorial.md), [COO Kai-team build](https://ms-contacts.coo-kai.jp/) [, Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [Discovery bene fits](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-global-reader-role"></a>Nieuwe globale rol van Azure AD-lezer

**Type:** Nieuwe functie **service categorie:** Azure AD roles- **Product mogelijkheden:** Access Control

Vanaf 24 september 2019 beginnen we met het implementeren van een nieuwe Azure Active Directory-rol (AD) met de naam Global Reader. Deze implementatie begint met productie-en mondiale Cloud klanten (GCC), die wereld wijd in oktober wordt voltooid.

De rol van algemene lezer is het alleen-lezen equivalent van de globale beheerder. Gebruikers met deze rol kunnen instellingen en beheer gegevens in Microsoft 365 services lezen, maar kunnen geen beheer acties uitvoeren. We hebben de algemene rol lezer gemaakt om het aantal globale beheerders in uw organisatie te verminderen. Omdat globale beheerders accounts krachtig en kwetsbaar zijn voor aanvallen, raden wij aan dat u minder dan vijf globale beheerders hebt. We raden u aan de rol van globale lezer te gebruiken voor het plannen, controleren of onderzoeken. We raden u ook aan de rol van globale lezer te gebruiken in combi natie met andere beperkte beheerders rollen, zoals Exchange Administrator, om aan de slag te gaan zonder dat hiervoor de rol van globale beheerder is vereist.

De rol van algemene lezer werkt met de nieuwe Microsoft 365 beheer centrum, het Exchange-beheer centrum, teams beheer centrum, Security Center, compliance Center, Azure AD-beheer centrum en het beheer centrum voor Apparaatbeheer.

>[!NOTE]
> Aan het begin van de open bare preview-versie werkt de rol van globale lezer niet met: share point, Privileged Access Management, Klanten-lockbox, gevoeligheids labels, teams levenscyclus, teams rapporteren & oproep analyse, teams IP-telefoon Apparaatbeheer en teams-app-catalogus.

Zie [machtigingen voor beheerdersrol in azure Active Directory](../roles/permissions-reference.md)voor meer informatie.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Toegang tot een on-premises rapport server vanuit uw Power BI-Mobiel-app met behulp van Azure Active Directory-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Dankzij de nieuwe integratie tussen de Power BI mobiele app en Azure AD-toepassingsproxy kunt u zich veilig aanmelden bij de mobiele app van Power BI en de rapporten van uw organisatie weer geven die worden gehost op de on-premises Power BI Report Server.

Zie de [Power bi-site](https://powerbi.microsoft.com/mobile/)voor meer informatie over de Power bi-mobiel-app, inclusief de plek waar u de app kunt downloaden. Zie voor meer informatie over het instellen van de Power BI mobiele app met Azure AD-toepassingsproxy [externe toegang inschakelen voor Power bi-mobiel met azure AD-toepassingsproxy](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Er is een nieuwe versie van de Power shell-module AzureADPreview beschikbaar

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nieuwe versie van Azure AD Connect

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

We hebben een bijgewerkte versie van Azure AD Connect uitgebracht voor klanten met automatische upgrades. Deze nieuwe versie bevat verschillende nieuwe functies, verbeteringen en oplossingen voor problemen. Zie [Azure AD Connect: release geschiedenis](../hybrid/reference-connect-version-history.md#14250)van de versie voor meer informatie over deze nieuwe versie.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA)-server, versie 8.0.2 is nu beschikbaar

**Type:** Vaste **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Als u een bestaande klant bent die een MFA-server heeft geactiveerd voor 1 juli 2019, kunt u nu de nieuwste versie van de MFA-server (versie 8.0.2) downloaden. In deze nieuwe versie:

- Als er een probleem is opgelost, wordt een e-mail bericht verzonden naar de gebruiker wanneer de Azure AD-synchronisatie een gebruiker van uitgeschakeld wijzigt in ingeschakeld.

- Er is een probleem opgelost waardoor klanten kunnen upgraden, terwijl ze de functionaliteit van Tags blijven gebruiken.

- De land code Kosovo (+ 383) is toegevoegd.

- Eenmalige controle logboek registratie voor bypass is toegevoegd aan MultiFactorAuthSvc. log.

- Verbeterde prestaties voor de Web Service SDK.

- Andere kleine bugs zijn opgelost.

Vanaf 1 juli 2019 heeft micro soft de MFA-server voor nieuwe implementaties gestopt. Nieuwe klanten die multi-factor Authentication vereisen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud. Zie [een Cloud implementatie van Azure AD multi-factor Authentication plannen](../authentication/howto-mfa-getstarted.md)voor meer informatie.

---

## <a name="august-2019"></a>Augustus 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Uitgebreide zoek-, filter-en sorteer bewerkingen voor groepen zijn beschikbaar in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We zijn blij met het aankondigen van de open bare preview-Beschik baarheid van de uitgebreide ervaringen met groepen in de Azure AD-Portal. Deze verbeteringen helpen u bij het beheren van groepen en leden lijsten door het volgende te bieden:

- Geavanceerde zoek functies, zoals subtekenreeksen, zoeken in groepen-lijsten.
- Geavanceerde filter-en sorteer opties voor de lijsten leden en eigen aren.
- Nieuwe zoek mogelijkheden voor lijsten van leden en eigen aren.
- Nauw keurigere groeps aantallen voor grote groepen.

Zie [groepen beheren in de Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)voor meer informatie.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Er zijn nieuwe aangepaste rollen beschikbaar voor app-registratie beheer (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD roles- **Product mogelijkheden:** Access Control

Aangepaste rollen (beschikbaar met een Azure AD P1-of P2-abonnement) kunnen u nu voorzien van gedetailleerde toegang, door roldefinities te maken met specifieke machtigingen en deze rollen vervolgens toe te wijzen aan specifieke resources. Op dit moment kunt u aangepaste rollen maken met behulp van machtigingen voor het beheren van app-registraties en vervolgens de rol toewijzen aan een specifieke app. Zie [aangepaste beheerders rollen in azure Active Directory (preview)](../roles/custom-overview.md)voor meer informatie over aangepaste rollen.

Als u aanvullende machtigingen of bronnen nodig hebt die momenteel niet worden weer gegeven, kunt u feedback verzenden naar onze [Azure-feedback site](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) . we voegen uw aanvraag toe aan onze update-weg kaart.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Met nieuwe inrichtings Logboeken kunt u de implementatie van de app-inrichting controleren en problemen oplossen (open bare preview)

**Type:** Nieuwe functie **service categorie:** product inrichting van de app **:** Identity Lifecycle Management

Er zijn nieuwe inrichtings logboeken beschikbaar om u te helpen bij het controleren en oplossen van problemen met de implementatie van gebruikers en groepen. Deze nieuwe logboek bestanden bevatten informatie over:

- Welke groepen zijn gemaakt in [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Welke rollen zijn geïmporteerd uit [Amazon Web Services (AWS)](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-amazon-web-services-aws)
- De werk nemers die niet zijn geïmporteerd uit [workday](../saas-apps/workday-inbound-tutorial.md)

Zie [inrichtings rapporten in de Azure Active Directory Portal (preview)](../reports-monitoring/concept-provisioning-logs.md)voor meer informatie.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nieuwe beveiligings rapporten voor alle Azure AD-beheerders (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Standaard hebben alle Azure AD-beheerders binnenkort toegang tot moderne beveiligings rapporten in azure AD. Tot het einde van september kunt u de banner aan de bovenkant van de moderne beveiligings rapporten gebruiken om terug te keren naar de oude rapporten.

De moderne beveiligings rapporten bieden extra mogelijkheden van de oudere versies, waaronder:

- Geavanceerd filteren en sorteren
- Bulk acties, zoals het negeren van gebruikers Risico's
- Bevestiging van aangetast of veilige entiteiten
- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke
- Nieuwe detecties met een risico (beschikbaar voor Azure AD Premium abonnees)

Zie [Risk ante gebruikers](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [Risk ante aanmeldingen](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)en [risico detectie](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)voor meer informatie.

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Door de gebruiker toegewezen beheerde identiteit is beschikbaar voor Virtual Machines en Virtual Machine Scale Sets (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** beheerde identiteiten voor Azure-resources **product mogelijkheden:** ontwikkelaars ervaring

Door de gebruiker toegewezen beheerde identiteiten zijn nu algemeen beschikbaar voor Virtual Machines en Virtual Machine Scale Sets. Als onderdeel hiervan kan Azure een identiteit maken in de Azure AD-Tenant die wordt vertrouwd door het abonnement en kan worden toegewezen aan een of meer Azure-service-exemplaren. Zie [Wat is beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md)voor meer informatie over door de gebruiker toegewezen beheerde identiteiten.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gebruikers kunnen hun wacht woord opnieuw instellen met behulp van een mobiele app of een hardware-token (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

Gebruikers die een mobiele app hebben geregistreerd bij uw organisatie kunnen nu hun eigen wacht woord opnieuw instellen door een melding van de Microsoft Authenticator-app goed te keuren of door een code uit hun mobiele app of hardware-token in te voeren.

Zie [hoe het werkt: Azure AD self-service password reset](../authentication/concept-sspr-howitworks.md)(Engelstalig) voor meer informatie. Zie [uw eigen werk-of school wachtwoord opnieuw instellen-overzicht](../user-help/active-directory-passwords-reset-register.md)voor meer informatie over de gebruikers ervaring.

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET negeert de gedeelde cache van MSAL.NET voor namens een scenario

**Type:** Vaste **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf Azure AD Authentication Library (ADAL.NET) versie 5.0.0-Preview moeten app-ontwikkel aars [één cache per account serialiseren voor web-apps en Web-api's](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Anders is het mogelijk dat sommige scenario's die gebruikmaken van de [voor-](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) en hand leiding voor Java, samen met een aantal specifieke gebruiks voorbeelden van `UserAssertion` , een uitbrei ding van bevoegdheden kunnen opleveren. Om dit beveiligings probleem te voor komen, negeert ADAL.NET nu de micro soft-verificatie bibliotheek voor dotnet (MSAL.NET) gedeelde cache voor namens een scenario.

Zie [Azure Active Directory beveiligingslek met betrekking tot misbruik van bevoegdheden voor verificatie bibliotheek](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)voor meer informatie over dit probleem.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-augustus 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2019 augustus hebben we deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Civic-platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos OPS Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport Inativ Portal (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy-aanwezigheid](../saas-apps/academy-attendance-tutorial.md), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite endpoint backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync door CinCom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliverable. media &trade; Portal](https://portal.deliver.media), [Frontline onderwijs](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [bekijken op kleuren](../saas-apps/watch-by-colors-tutorial.md), [harnas](../saas-apps/harness-tutorial.md), [EAB Navigate strategisch Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Er zijn nieuwe versies van de Power shell-en AzureADPreview-AzureAD

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

Er zijn nieuwe updates beschikbaar voor de AzureAD-en AzureAD preview Power shell-modules:

- Er is een nieuwe `-Filter` para meter toegevoegd aan de `Get-AzureADDirectoryRole` para meter in de AzureAD-module. Met deze para meter kunt u filteren op de Directory functies die door de cmdlet worden geretourneerd.
- Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module, waarmee u aangepaste rollen kunt definiëren en toewijzen in azure AD, waaronder:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Verbeteringen in de gebruikers interface van de opbouw functie voor dynamische groeps regels in de Azure Portal

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Er zijn enkele verbeteringen in de gebruikers interface aangebracht in de opbouw functie voor dynamische groeps regels, die beschikbaar is in de Azure Portal, zodat u gemakkelijk een nieuwe regel kunt instellen of bestaande regels kunt wijzigen. Met deze ontwerp verbetering kunt u regels maken met Maxi maal vijf expressies, in plaats van slechts één. Daarnaast hebben we de lijst met eigenschappen van het apparaat bijgewerkt om afgeschafte apparaateigenschappen te verwijderen.

Zie [regels voor dynamische lidmaatschap beheren](../enterprise-users/groups-dynamic-membership.md)voor meer informatie.

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Er is een nieuwe Microsoft Graph app-machtiging beschikbaar voor gebruik met toegangs beoordelingen

**Type:** Gewijzigde functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** Identity governance

We hebben een nieuwe Microsoft Graph app-machtiging geïntroduceerd, `AccessReview.ReadWrite.Membership` waarmee apps automatisch toegangs Beoordelingen voor groepslid maatschappen en app-toewijzingen kunnen maken en ophalen. Deze machtiging kan worden gebruikt door uw geplande taken of als onderdeel van uw automatisering zonder dat hiervoor een aangemelde gebruikers context nodig is.

Zie voor meer informatie het [maken van Azure AD-toegangs beoordelingen met Microsoft Graph app-machtigingen met Power shell-blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar voor overheids Cloud instanties in Azure Monitor

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We zijn enthousiast dat Azure AD-activiteiten logboeken nu beschikbaar zijn voor instanties van overheids-Clouds in Azure Monitor. U kunt nu Azure AD-logboeken naar uw opslag account of een Event Hub verzenden om te integreren met uw SIEM-hulpprogram ma's, zoals [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)en [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Voor meer informatie over het instellen van Azure Monitor raadpleegt u [Azure AD-activiteiten Logboeken in azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Werk uw gebruikers bij naar de nieuwe, verbeterde ervaring voor beveiligings informatie

**Type:** Gewijzigde functie **service categorie:**  verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Op 25 september 2019 worden de oude, niet-verbeterde beveiligings gegevens voor het registreren en beheren van gebruikers beveiligings gegevens uitgeschakeld en wordt alleen de nieuwe, [verbeterde versie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)ingeschakeld. Dit betekent dat uw gebruikers de oude ervaring niet langer kunnen gebruiken.

Zie onze [beheerders documentatie](../authentication/concept-registration-mfa-sspr-combined.md) en onze [gebruikers documentatie](../user-help/security-info-setup-signin.md)voor meer informatie over de verbeterde ervaring met beveiligings informatie.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Als u deze nieuwe ervaring wilt inschakelen, moet u het volgende doen:

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder.

2. Ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster beheren**.

3. Selecteer in de sectie **gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiligings gegevens-uitgebreid** gebied **geselecteerd**, en kies vervolgens een groep gebruikers of kies **alle** om deze functie in te scha kelen voor alle gebruikers in de Tenant.

4. In de * * gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiliging * * info * * * * *. Selecteer **geen**.

5. Sla uw wijzigingen op.

    Nadat u de instellingen hebt opgeslagen, hebt u geen toegang meer tot de oude beveiligings informatie.

>[!Important]
>Als u deze stappen vóór 25 september 2019 niet voltooit, wordt uw Azure Active Directory-Tenant automatisch ingeschakeld voor de verbeterde ervaring. Als u vragen hebt, kunt u contact met ons opnemen via registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Verificatie aanvragen via POST-aanmeldingen worden strikter gevalideerd

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** standaarden

Vanaf 2 september 2019 worden verificatie aanvragen via de POST-methode strikt gevalideerd op basis van de HTTP-standaarden. In het bijzonder worden spaties en dubbele aanhalings tekens (") niet meer verwijderd uit de waarden van het aanvraag formulier. Deze wijzigingen worden niet verwacht om bestaande clients te verstoren en ervoor te zorgen dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden afgehandeld.

Zie voor meer informatie de [meldingen over Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)-onderbrekingen.

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plannen voor wijziging: update van Application proxy-service om alleen TLS 1,2 te ondersteunen

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Om u te helpen met onze krach tigste versleuteling, gaan we de toegang tot de toepassings proxy service beperken tot alleen TLS 1,2-protocollen. Deze beperking wordt eerst doorgevoerd naar klanten die al gebruikmaken van TLS 1,2-protocollen, zodat u de impact niet kunt zien. De afronding van de protocollen TLS 1,0 en TLS 1,1 is voltooid op 31 augustus 2019. Klanten die nog TLS 1,0 en TLS 1,1 gebruiken, ontvangen een geavanceerde kennisgeving om deze wijziging voor te bereiden.

Als u de verbinding met de service toepassings proxy tijdens deze wijziging wilt behouden, kunt u het beste controleren of uw combi Naties van client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1,2. We raden u ook aan om ervoor te zorgen dat alle client systemen die door uw werk nemers worden gebruikt, toegang hebben tot apps die zijn gepubliceerd via de Application proxy-service.

Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md)voor meer informatie.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plannen voor wijziging: ontwerp updates zijn beschikbaar voor de toepassings galerie

**Type:** Planning voor **service categorie wijzigen:** Enter prise apps **product Capability:** SSO

Nieuwe wijzigingen in de gebruikers interface zijn afkomstig van het ontwerp van de **toevoegen vanuit het gebied Galerie** van de Blade **een toepassing toevoegen** . Met deze wijzigingen kunt u eenvoudig uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect Connect, Security Assertion Markup Language (SAML) en een wacht woord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plannen voor wijziging: het IP-adres van de MFA-server verwijderen uit het Office 365 IP-adres

**Type:** Planning voor **service categorie wijzigen:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Het IP-adres van de MFA-server wordt verwijderd uit het [Office 365 IP-adres en de URL-webservice](/office365/enterprise/office-365-ip-web-service). Als u momenteel afhankelijk bent van deze pagina's om uw firewall instellingen bij te werken, moet u ervoor zorgen dat u ook de lijst met IP-adressen die worden beschreven in de sectie **azure multi-factor Authentication-Server-firewall vereisten** van het artikel aan de slag [met Azure multi-factor Authentication-Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor alleen app-tokens moet de client-app bestaan in de resource Tenant

**Type:** Vaste **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Op 26 juli 2019 hebben we gewijzigd hoe we alleen app-tokens bieden via de [client referenties verlenen](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de Tenant bevindt. We hebben dit gedrag bijgewerkt, waardoor enkele Tenant bronnen, ook wel web-Api's genoemd, alleen kunnen worden aangeroepen door client-apps die voor komen in de resource-Tenant.

Als uw app zich niet in de resource Tenant bevindt, wordt er een fout bericht weer gegeven waarin wordt vermeld, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` om dit probleem op te lossen, moet u de client app Service-Principal in de Tenant maken met behulp van het [beheerders toestemmings eindpunt](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) of [via Power shell](../develop/howto-authenticate-service-principal-powershell.md), zodat uw Tenant de app toestemming heeft gegeven om binnen de Tenant te werken.

Zie [Wat is er nieuw voor verificatie?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)voor meer informatie.

> [!NOTE]
> Bestaande toestemming tussen de client en de API blijft niet vereist. Apps moeten nog steeds hun eigen autorisatie controles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wacht woord voor Azure AD met behulp van FIDO2-beveiligings sleutels

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligings sleutels voor de gebruikers en groepen van hun organisatie. Eind gebruikers kunnen hun beveiligings sleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun micro soft-accounts op websites, terwijl ze op FIDO apparaten werken, en aanmelden bij hun Azure AD-Windows 10-apparaten.

Zie voor meer informatie [aanmelden zonder wacht woord inschakelen voor Azure AD (preview)](../authentication/concept-authentication-passwordless.md) voor informatie die betrekking heeft op de beheerder en [Stel beveiligings informatie in voor het gebruik van een beveiligings sleutel (preview)](../user-help/security-info-setup-security-key.md) voor informatie die aan de eind gebruiker is gerelateerd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in Azure AD-app galerie-juli 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juli 2019 zijn deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Ungerboeck software](../saas-apps/ungerboeck-software-tutorial.md), [helder patroon Omnichannel contact centrum](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [slimme Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex compliance &trade; Office](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [hele hype](../saas-apps/hype-tutorial.md), [abstract](../saas-apps/abstract-tutorial.md), [schuinis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nieuwe Azure AD Domain Services servicetag voor de netwerk beveiligings groep

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Als u veel lijsten met IP-adressen en-bereiken niet meer wilt beheren, kunt u de nieuwe **AzureActiveDirectoryDomainServices** -netwerk servicetag gebruiken in uw Azure-netwerk beveiligings groep om inkomend verkeer naar uw Azure AD Domain Services subnet van het virtuele netwerk te beveiligen.

Zie [netwerk beveiligings groepen voor Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)voor meer informatie over deze nieuwe servicetag.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services als kritisch inzicht verschaffen door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD Domain Service-Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](../../active-directory-domain-services/security-audit-events.md)voor meer informatie.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nieuwe verificatie methoden Usage & Insights (open bare preview)

**Type:** Nieuwe functie **service categorie:** self-service voor het opnieuw instellen van wacht woorden **:** bewaking & rapportage

Met de nieuwe verificatie methoden & Insights-rapporten kunt u zien hoe functies zoals Azure AD Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden worden geregistreerd en gebruikt in uw organisatie, inclusief het aantal geregistreerde gebruikers voor elke functie, hoe vaak de selfservice voor wachtwoord herstel wordt gebruikt om wacht woorden opnieuw in te stellen, en op basis van welke methode het opnieuw moet worden ingesteld.

Zie het gebruik van de [verificatie methoden & Insights (preview)](../authentication/howto-authentication-methods-usage-insights.md)voor meer informatie.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nieuwe beveiligings rapporten zijn beschikbaar voor alle Azure AD-beheerders (open bare preview)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Alle Azure AD-beheerders kunnen nu het vaandel selecteren boven aan bestaande beveiligings rapporten, zoals de gebruikers die zijn **gemarkeerd voor risico** rapport, om de nieuwe beveiligings ervaring te gebruiken, zoals wordt weer gegeven in de rapporten **Risk ante gebruikers** en **Risk ante aanmeldingen** . Na verloop van tijd worden alle beveiligings rapporten van oudere versies naar de nieuwe versies verplaatst, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulk acties, zoals het negeren van gebruikers Risico's

- Bevestiging van aangetast of veilige entiteiten

- Risico status, waaronder: risico, genegeerd, hersteld en bevestigde schadelijke

Zie rapport met [Risk ante gebruikers](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) en [rapporten met Risk ante aanmeldingen](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)voor meer informatie.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligings controles voor Azure AD Domain Services (open bare preview)

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Met trots kondigen we de release van de beveiligings controle van Azure AD domain service aan voor open bare preview. Met beveiligings controle kunt u uw verificatie services als kritisch inzicht verschaffen door beveiligings controle gebeurtenissen te streamen naar gerichte resources, waaronder Azure Storage, Azure Log Analytics-werk ruimten en Azure Event hub, met behulp van de Azure AD Domain Service-Portal.

Zie [beveiligings controles voor Azure AD Domain Services inschakelen (preview)](../../active-directory-domain-services/security-audit-events.md)voor meer informatie.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nieuwe B2B direct Federation met behulp van SAML/WS-voeder (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Direct Federatie helpt u bij het werken met partners waarvan de IT-beheerde identiteits oplossing niet Azure AD is, door te werken met identiteits systemen die ondersteuning bieden voor de SAML-of WS-Fed-standaarden. Nadat u een directe Federatie relatie met een partner hebt ingesteld, kan elke nieuwe gast gebruiker die u uit dat domein uitnodigt met behulp van hun bestaande organisatie account samen werken, waardoor de gebruikers ervaring voor uw gasten naadloos wordt.

Zie voor meer informatie [directe Federatie met AD FS en providers van derden voor gast gebruikers (preview)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nieuwe controle op dubbele groeps namen in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Wanneer u nu een groeps naam vanuit de Azure AD-Portal maakt of bijwerkt, wordt er een controle uitgevoerd om te zien of u een bestaande groeps naam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd om uw naam te wijzigen.

Zie [groepen beheren in de Azure AD-Portal](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)voor meer informatie.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD biedt nu ondersteuning voor statische query parameters in antwoord-Uri's (redirect)

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-apps kunnen nu Uri's voor beantwoorden (omleiden) registreren en gebruiken met statische query parameters (bijvoorbeeld `https://contoso.com/oauth2?idp=microsoft` ) voor OAuth 2,0-aanvragen. De statische query parameter is onderhevig aan de teken reeks die overeenkomt met antwoord-Uri's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde teken reeks is die overeenkomt met de door de URL gedecodeerde omleidings-URI, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de gehele teken reeks gebruikt om de gebruiker te omleiden, inclusief de para meter static query.

Dynamische antwoord-Uri's zijn nog niet toegestaan omdat ze een beveiligings risico vertegenwoordigen en niet kunnen worden gebruikt om status informatie over een verificatie aanvraag te houden. Voor dit doel gebruikt u de `state` para meter.

Momenteel zijn de app-registratie schermen van de Azure Portal nog steeds blok keren query parameters. U kunt het app-manifest echter hand matig bewerken om query parameters toe te voegen en te testen in uw app. Zie [Wat is er nieuw voor verificatie?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)voor meer informatie.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteiten Logboeken (MS Graph-Api's) voor Azure AD zijn nu beschikbaar via Power shell-cmdlets

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Met trots kondigen we aan dat Azure AD-activiteiten Logboeken (rapporten over controles en aanmeldingen) nu beschikbaar zijn via de Azure AD Power shell-module. U kunt voorheen uw eigen scripts maken met behulp van MS Graph API-eind punten en nu hebben we die mogelijkheid tot Power shell-cmdlets uitgebreid.

Zie [Azure AD Power shell-cmdlets voor rapportage](../reports-monitoring/reference-powershell-reporting.md)voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Bijgewerkte filter besturings elementen voor controle-en aanmeld Logboeken in azure AD

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

De controle-en aanmeldings logboek rapporten zijn bijgewerkt, zodat u nu verschillende filters kunt Toep assen zonder ze als kolommen toe te voegen aan de rapport schermen. Daarnaast kunt u bepalen hoeveel filters u wilt weer geven op het scherm. Deze updates werken allemaal samen om uw rapporten gemakkelijker te kunnen lezen en zo meer te bereiken aan uw behoeften.

Zie [controle logboeken filteren](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) en [aanmeldings activiteiten filteren](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)voor meer informatie over deze updates.

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nieuwe riskDetections-API voor Microsoft Graph (open bare preview)

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

We zijn blij met het aankondigen van de nieuwe riskDetections-API voor Microsoft Graph nu in open bare preview. U kunt deze nieuwe API gebruiken om een lijst weer te geven met de identiteits beveiliging van de gebruiker en de detectie van de aanmeldings Risico's van uw organisatie. U kunt deze API ook gebruiken om efficiënter een query uit te voeren op uw risico detecties, inclusief details over het detectie type, de status, het niveau en meer.

Zie de [referentie documentatie voor API voor risico detectie](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juni 2019 hebben we deze 22 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [helper helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML-verificatie](../saas-apps/cyberark-saml-authentication-tutorial.md), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager voor Oracle retail merchandising, Oracle Access Manager voor Oracle E-Business Suite, Oracle IDCS voor E-Business Suite, Oracle IDCS voor People Soft, Oracle IDCS voor JD Edwards

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikers accounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor deze nieuwe, geïntegreerde apps:

- [In- en uitzoomen](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>De real-time voortgang van de Azure AD-inrichtings service weer geven

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product Capability:** Identity Lifecycle Management

We hebben de Azure AD-inrichtings ervaring bijgewerkt zodat er een nieuwe voortgangs balk wordt weer gegeven waarin u kunt zien hoe ver u het proces voor het inrichten van gebruikers hebt. Deze bijgewerkte ervaring bevat ook informatie over het aantal gebruikers dat is ingericht tijdens de huidige cyclus, en hoeveel gebruikers tot nu toe zijn ingericht.

Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>De huis stijl van het bedrijf wordt nu weer gegeven bij afmelden en fout schermen

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD is bijgewerkt, zodat de huis stijl van uw bedrijf nu wordt weer gegeven op de schermen voor afmelden en fout en op de aanmeldings pagina. U hoeft niets te doen om deze functie in te scha kelen. Azure AD maakt gewoon gebruik van de activa die u al hebt ingesteld in het **huis stijlgebied** van de Azure Portal.

Zie [huisstijl toevoegen aan de Azure Active Directory pagina's van uw organisatie](./customize-branding.md)voor meer informatie over het instellen van de huis stijl van uw bedrijf.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>De Azure Multi-Factor Authentication-Server (MFA) is niet meer beschikbaar voor nieuwe implementaties

**Type:** Afgeschafte **service categorie:** MFA- **product mogelijkheid:** identiteits beveiliging & beveiliging

Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication in hun organisatie willen vereisen, moeten nu gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud. Klanten die de MFA-server vóór 1 juli hebben geactiveerd, zien geen wijziging. U kunt nog steeds de nieuwste versie downloaden, toekomstige updates ophalen en activerings referenties genereren.

Zie [aan de slag met de Azure-multi-factor Authentication-Server](../authentication/howto-mfaserver-deploy.md)voor meer informatie. Voor meer informatie over Azure AD-Multi-Factor Authentication in de Cloud, raadpleegt u [een implementatie van Azure ad multi-factor Authentication op basis van de Cloud plannen](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Mei 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Service wijziging: toekomstige ondersteuning voor alleen TLS 1,2-protocollen op de Application proxy-service

**Type:** Planning voor wijziging **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Om de beste versleuteling voor onze klanten te bieden, beperken we de toegang tot alleen TLS 1,2-protocollen op de Application proxy-service. Deze wijziging wordt geleidelijk doorgevoerd naar klanten die al alleen gebruikmaken van TLS 1,2-protocollen, zodat u geen wijzigingen ziet.

De afschaffing van TLS 1,0 en TLS 1,1 gebeurt op 31 augustus 2019, maar we bieden nog meer geavanceerde kennisgevingen, dus u hebt de tijd om deze wijziging voor te bereiden. Om deze wijziging voor te bereiden, moet u ervoor zorgen dat de combi Naties van client-server en browser server, inclusief alle clients die uw gebruikers gebruiken om toegang te krijgen tot apps die zijn gepubliceerd via toepassings proxy, worden bijgewerkt met het TLS 1,2-protocol om de verbinding met de Application proxy-service te onderhouden. Zie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites)voor meer informatie.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Gebruik het rapport gebruik en inzichten om uw app-gerelateerde aanmeldings gegevens weer te geven

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

U kunt nu het rapport gebruik en inzichten gebruiken, dat zich bevindt in het gebied **bedrijfs toepassingen** van de Azure Portal, om een toepassings gerichte weer gave van uw aanmeldings gegevens te verkrijgen, met inbegrip van informatie over:

- Meestgebruikte apps voor uw organisatie

- Apps met de meeste mislukte aanmeldingen

- Meest voorkomende aanmeld fouten voor elke app

Zie voor meer informatie over deze functie [het rapport gebruik en inzichten in de Azure Active Directory Portal](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiseer uw gebruikers inrichting voor Cloud-apps met behulp van Azure AD

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** monitoring & Reporting

Volg deze nieuwe zelf studies om de Azure AD-inrichtings service te gebruiken voor het automatiseren van het maken, verwijderen en bijwerken van gebruikers accounts voor de volgende Cloud-apps:

- [Voldoen aan](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

U kunt deze nieuwe Dropbox- [zelf studie](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)ook volgen, die informatie bevat over het inrichten van groeps objecten.

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Beveiligde score voor identiteit is nu beschikbaar in azure AD (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** n.v.t. **product mogelijkheid:** identiteits beveiliging & beveiliging

U kunt nu uw identiteits beveiligings postuur bewaken en verbeteren met behulp van de functie voor beveiligde scores voor identiteiten in azure AD. De functie voor het beveiligen van identiteiten maakt gebruik van één dash board waarmee u het volgende kunt doen:

- Objectief meet uw identiteits beveiligings postuur op basis van een score tussen 1 en 223.

- Uw verbeteringen voor identiteits beveiliging plannen

- Bekijk het succes van uw beveiligings verbeteringen

Zie [Wat is de identiteit beveiligde Score in azure Active Directory?](./identity-secure-score.md)voor meer informatie over de functie voor identiteits beveiliging.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nieuwe App-registraties-ervaring is nu beschikbaar (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** authenticaties (aanmeldingen) **product mogelijkheid:** ontwikkelaars ervaring

De nieuwe [app-registraties](https://aka.ms/appregistrations) -ervaring is nu in algemene Beschik baarheid. Deze nieuwe ervaring omvat alle belang rijke functies van de Azure Portal en de portal voor toepassings registratie en verbetert deze.

- **Betere app-beheer.** In plaats van uw apps te zien op verschillende portals, kunt u nu al uw apps op één locatie bekijken.

- **Vereenvoudigde registratie van de app.** Van de verbeterde navigatie-ervaring tot de uitgebreide mogelijkheden voor het selecteren van machtigingen is het nu eenvoudiger om uw apps te registreren en te beheren.

- **Meer gedetailleerde informatie.** U kunt meer informatie vinden over uw app, inclusief Quick start-gidsen en meer.

Zie voor meer informatie [micro soft Identity platform](../develop/index.yml) en de [app-registraties-ervaring is nu algemeen beschikbaar.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) aankondiging van blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nieuwe mogelijkheden die beschikbaar zijn in de Risk ante gebruikers-API voor identiteits beveiliging

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Het is blij dat u de Risk ante gebruikers API kunt gebruiken om de risico geschiedenis van gebruikers op te halen, Risk ante gebruikers te sluiten en gebruikers te bevestigen dat ze zijn aangetast. Met deze wijziging kunt u de risico status van uw gebruikers efficiënter bijwerken en inzicht krijgen in hun risico geschiedenis.

Zie de [naslag documentatie voor Risk ante gebruikers-API](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In mei 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [real links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [deplayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [marketo Sales](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [Outsystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), [Quantum werkplek](../saas-apps/quantum-workplace-tutorial.md), [kobalt](../saas-apps/cobalt-tutorial.md), [webmethodes API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [levens](../saas-apps/foodee-tutorial.md)werk, [MyVR](../saas-apps/myvr-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbeterde functies voor het maken en beheren van groepen in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

We hebben verbeteringen aangebracht in de groeps-gerelateerde ervaringen in de Azure AD-Portal. Met deze verbeteringen kunnen beheerders de lijsten met groepen, leden lijsten en aanvullende opties voor het maken beter beheren.

De verbeteringen zijn onder andere:

- Basis filtering op lidmaatschaps type en groeps type.

- Toevoeging van nieuwe kolommen, zoals bron-en e-mail adres.

- De mogelijkheid om meerdere groepen, leden en eigen aars lijsten te selecteren voor eenvoudige verwijdering.

- Mogelijkheid om een e-mail adres te kiezen en eigen aren toe te voegen tijdens het maken van de groep.

Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](./active-directory-groups-create-azure-portal.md) voor meer informatie.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](../enterprise-users/groups-naming-policy.md)voor meer informatie.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Er zijn nu Microsoft Graph API-eind punten beschikbaar voor Azure AD-activiteiten Logboeken (algemene Beschik baarheid)

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We zijn blij met het aankondigen van de algemene Beschik baarheid van Microsoft Graph API-eind punten ondersteuning voor Azure AD-activiteiten Logboeken. Met deze versie kunt u nu versie 1,0 van zowel de Azure AD-audit logboeken en de Api's voor aanmeld Logboeken gebruiken.

Zie overzicht van de [API voor Azure AD-controle logboeken](/graph/api/resources/azure-ad-auditlog-overview)voor meer informatie.

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Beheerders kunnen nu voorwaardelijke toegang gebruiken voor het gecombineerde registratie proces (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Beheerders kunnen nu beleid voor voorwaardelijke toegang maken voor gebruik door de gecombineerde registratie pagina. Dit geldt ook voor het Toep assen van beleid om registratie in te stellen als:

- Gebruikers bevinden zich in een vertrouwd netwerk.

- Gebruikers zijn een laag aanmeldings risico.

- Gebruikers bevinden zich op een beheerd apparaat.

- Gebruikers komen overeen met de gebruiks voorwaarden van de organisatie.

Zie voor meer informatie over voorwaardelijke toegang en het opnieuw instellen van wacht woorden de [voorwaardelijke toegang voor het blog bericht over gecombineerde MFA-en wachtwoord herstel voor Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Zie [beleid voor voorwaardelijke toegang voor gecombineerde registratie](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration)voor meer informatie over beleids regels voor voorwaardelijke toegang voor het gecombineerde registratie proces. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](../conditional-access/terms-of-use.md)voor meer informatie over de functie gebruiks voorwaarden van Azure AD.

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nieuwe Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van Azure AD Identity Protection

**Type:** Nieuwe functie **service categorie:** Azure AD Identity Protection **product capaciteit:** identiteits beveiliging & beveiliging

Azure AD Threat Intelligence-detectie is nu beschikbaar als onderdeel van de bijgewerkte functie Azure AD Identity Protection. Deze nieuwe functionaliteit helpt bij het aanduiden van ongebruikelijke gebruikers activiteit voor een specifieke gebruiker of activiteit die consistent is met bekende aanvals patronen gebaseerd op de interne en externe informatie bronnen van micro soft.

Voor meer informatie over de vernieuwde versie van Azure AD Identity Protection raadpleegt u de [vier belangrijkste uitbrei dingen van de Azure AD Identity Protection nu in de open bare preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) -blog staan en [wat Azure Active Directory Identity Protection (vernieuwd)?](../identity-protection/overview-identity-protection.md) . Voor meer informatie over de detectie van Azure AD Threat Intelligence raadpleegt u het artikel [Azure Active Directory Identity Protection risico detectie](../identity-protection/concept-identity-protection-risks.md) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Het beheer van rechten van Azure AD is nu beschikbaar (open bare preview)

**Type:** Nieuwe functie **service categorie:** Identity governance **product Capability:** Identity governance

Het beheer van rechten van Azure AD, nu in open bare preview, helpt klanten bij het delegeren van beheer van toegangs pakketten, waarmee wordt gedefinieerd hoe werk nemers en zakelijke partners toegang kunnen vragen, wie moet goed keuren en hoelang ze toegang hebben. Toegangs pakketten kunnen lidmaatschap beheren in azure AD en Office 365-groepen, roltoewijzingen in zakelijke toepassingen, en roltoewijzingen voor share point online-sites. Meer informatie over rechten beheer vindt u in het [overzicht van het beheer van rechten van Azure AD](../governance/entitlement-management-overview.md). Zie [Wat is Azure AD Identity governance?](../governance/identity-governance-overview.md)voor meer informatie over de breedte van Azure AD Identity governance functies, waaronder privileged Identity Management, toegangs beoordelingen en gebruiks voorwaarden.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Een naamgevings beleid configureren voor Office 365-groepen in de Azure AD-Portal (open bare preview)

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Beheerders kunnen nu een naamgevings beleid voor Office 365-groepen configureren met behulp van de Azure AD-Portal. Deze wijziging helpt bij het afdwingen van consistente naam conventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt het naamgevings beleid voor Office 365-groepen op twee verschillende manieren configureren:

- Voor voegsels of achtervoegsels definiëren die automatisch worden toegevoegd aan een groeps naam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groeps namen (bijvoorbeeld "CEO, Payroll, HR").

Zie [een naamgevings beleid afdwingen voor Office 365-groepen](../enterprise-users/groups-naming-policy.md)voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar in Azure Monitor (algemene Beschik baarheid)

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Om uw feedback over visualisaties met de activiteiten logboeken van Azure AD te helpen aanpakken, introduceren we een nieuwe inzichten-functie in Log Analytics. Deze functie helpt u bij het verkrijgen van inzichten over uw Azure AD-resources door gebruik te maken van onze interactieve sjablonen, werkmappen genoemd. Deze vooraf gemaakte werkmappen kunnen Details bieden voor apps of gebruikers, en omvatten:

- **Aanmeldingen.** Biedt Details voor apps en gebruikers, waaronder aanmeldings locatie, het besturings systeem in gebruik of de browser-client en-versie, en het aantal geslaagde of mislukte aanmeldingen.

- **Verouderde verificatie en voorwaardelijke toegang.** Biedt Details voor apps en gebruikers met behulp van verouderde verificatie, met inbegrip van Multi-Factor Authentication gebruik dat wordt geactiveerd door het beleid voor voorwaardelijke toegang, apps die gebruikmaken van beleid voor voorwaardelijke toegang, enzovoort.

- **Analyse van mislukte aanmeldingen.** Helpt u te bepalen of uw aanmeldings fouten optreden als gevolg van een gebruikers actie, beleids problemen of uw infra structuur.

- **Aangepaste rapporten.** U kunt nieuwe werkmappen maken of bewerken om de inzichten-functie voor uw organisatie aan te passen.

Zie [Azure monitor werkmappen gebruiken voor Azure Active Directory-rapporten](../reports-monitoring/howto-use-azure-monitor-workbooks.md)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-april 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In april 2019 hebben we deze 21 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks eenmalige aanmelding](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [Bank](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [afknot Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (op rollen gebaseerde SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [Monday.com](../saas-apps/mondaycom-tutorial.md), [surveymonkey Enter prise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nieuwe toegangs beoordelingen frequentie optie en selectie van meerdere rollen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** Identity governance

Met nieuwe updates in azure AD-toegangs beoordelingen kunt u het volgende doen:

- Wijzig de frequentie van uw toegangs beoordelingen in een **halfjaarlijkse**, naast de eerder bestaande opties wekelijks, maandelijks, elk kwar taal en jaarlijks.

- Selecteer meerdere Azure AD-en Azure-resource rollen bij het maken van één toegangs beoordeling. In dit geval worden alle rollen met dezelfde instellingen ingesteld en worden alle revisoren tegelijk op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](../governance/create-access-review.md)voor meer informatie over het maken van een toegangs beoordeling.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail waarschuwings systeem (en) worden overgezet, zodat er nieuwe e-mail gegevens worden verzonden voor sommige klanten

**Type:** Gewijzigde functie **service categorie:** AD Sync **product capaciteit:** platform

Azure AD Connect is bezig met het overstappen van onze e-mail waarschuwings systeem (s), waardoor sommige klanten een nieuwe e-mail afzender kunnen weer geven. Om dit op te lossen, moet u toevoegen `azure-noreply@microsoft.com` aan de acceptatie lijst van uw organisatie of u kunt geen belang rijke waarschuwingen ontvangen van uw Office 365, Azure of uw synchronisatie Services.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Wijzigingen in UPN-achtervoegsels zijn nu geslaagd tussen federatieve domeinen in Azure AD Connect

**Type:** **Categorie vaste service:** AD Sync **product capaciteit:** platform

U kunt nu het UPN-achtervoegsel van een gebruiker van een federatief domein wijzigen in een ander federatief domein in Azure AD Connect. Met deze oplossing wordt het FederatedDomainChangeError-fout bericht tijdens de synchronisatie cyclus niet meer ervaren of wordt er een e-mail bericht met de melding ' kan dit object niet bijwerken in Azure Active Directory, omdat het kenmerk [FederatedUser. UserPrincipalName] ongeldig is. Werk de waarde in uw lokale adreslijst Services bij.

Zie [probleemoplossings fouten tijdens de synchronisatie](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)voor meer informatie.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Verbeterde beveiliging met behulp van het beleid voor voorwaardelijke toegang op basis van app-beveiliging in azure AD (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Voorwaardelijke toegang op basis van app-beveiliging is nu beschikbaar via het beveiligings beleid voor het **vereisen van apps** . Dit nieuwe beleid helpt de beveiliging van uw organisatie te verbeteren door te voor komen dat:

- Gebruikers die toegang krijgen tot apps zonder een Microsoft Intune-licentie.

- Gebruikers kunnen geen Microsoft Intune beleid voor app-beveiliging ophalen.

- Gebruikers die toegang krijgen tot apps zonder een geconfigureerd Microsoft Intune beveiligings beleid voor apps.

Zie [app-beveiligings beleid vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](../conditional-access/app-protection-based-conditional-access.md)voor meer informatie.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang in micro soft Edge (open bare preview)

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

We hebben onze Azure AD-ondersteuning voor micro soft Edge uitgebreid, met inbegrip van nieuwe ondersteuning voor eenmalige aanmelding voor Azure AD en voorwaardelijke toegang. Als u Microsoft Intune Managed Browser eerder hebt gebruikt, kunt u nu gebruikmaken van micro soft Edge.

Zie voor meer informatie over het instellen en beheren van uw apparaten en apps met behulp van voorwaardelijke toegang [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](../conditional-access/require-managed-devices.md) en [goedgekeurde client-apps vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](../conditional-access/app-based-conditional-access.md). Voor meer informatie over het beheren van toegang met micro soft Edge met Microsoft Intune-beleid, Zie [Internet toegang beheren met een met Microsoft intune-beleid beveiligde browser](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Maart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Het Framework voor identiteits ervaring en aangepaste beleids ondersteuning in Azure Active Directory B2C is nu beschikbaar (GA)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu aangepaste beleids regels maken in Azure AD B2C, met inbegrip van de volgende taken, die op schaal en onder onze Azure-SLA worden ondersteund:

- U maakt en uploadt gebruikers trajecten voor aangepaste authenticatie door aangepaste beleids regels te gebruiken.

- Beschrijf de door de gebruiker geplaatste stapsgewijze stap-voor-stap als uitwisseling tussen claim providers.

- Definieer voorwaardelijke vertakking in de reis van de gebruiker.

- Trans formatie en toewijzing van claims voor gebruik in realtime beslissingen en communicatie.

- Gebruik REST API-services in uw eigen trajecten voor de gebruikers met aangepaste verificatie. Bijvoorbeeld met e-mail providers, CRMs en bedrijfsspecifieke autorisatie systemen.

- Communiceren met id-providers die voldoen aan het OpenIDConnect-protocol. Bijvoorbeeld met multi tenant Azure AD, sociale-account providers of twee ledige verificatie providers.

Zie voor meer informatie over het maken van aangepaste beleids regels [voor ontwikkel aars voor aangepast beleid in azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) en Lees [het blog bericht van Alex Simon, met inbegrip van](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)casestudy's.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-maart 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2019 maart hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [Medius flow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [controle systeem op basis van uitleg](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [PowerSchool prestatie zaken](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit horizonten](../saas-apps/confirmit-horizons-tutorial.md), [taa](../saas-apps/tas-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nieuwe Zscaler-en Atlassian-inrichtings connectors in de Azure AD Gallery-maart 2019

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Automatisch maken, bijwerken en verwijderen van gebruikers accounts voor de volgende apps:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler twee](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler drie](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen door middel van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Uw verwijderde Office 365-groepen herstellen en beheren in de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

U kunt nu uw verwijderde Office 365-groepen bekijken en beheren via de Azure AD-Portal. Met deze wijziging kunt u zien welke groepen beschikbaar zijn om te herstellen, en kunt u alle groepen die niet nodig zijn voor uw organisatie permanent verwijderen.

Zie [verlopen of verwijderde groepen herstellen](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)voor meer informatie.

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Eenmalige aanmelding is nu beschikbaar voor Azure AD SAML-beveiligde on-premises apps via toepassings proxy (open bare preview)

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

U kunt nu een SSO-ervaring (eenmalige aanmelding) bieden voor on-premises, met SAML geverifieerde apps en externe toegang tot deze apps via toepassings proxy. Zie voor meer informatie over het instellen van SAML SSO met uw on-premises apps [SAML eenmalige aanmelding voor on-premises toepassingen met toepassings proxy (preview)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Client-apps in de aanvraag lussen worden onderbroken om de betrouw baarheid en gebruikers ervaring te verbeteren

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Client-apps kunnen honderden dezelfde aanmeldings aanvragen binnen korte tijd onjuist uitgeven. Deze aanvragen, ongeacht of ze wel of niet zijn gelukt, nemen alle bijdragen aan een slechte gebruikers ervaring en verhoogde workloads voor de IDP, verg root de latentie voor alle gebruikers en verminderen de beschik baarheid van de IDP.

Deze update verzendt een `invalid_grant` fout melding: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` voor client-apps die dubbele aanvragen meerdere keren worden uitgevoerd gedurende een korte periode, buiten het bereik van de normale werking. Client-apps die dit probleem ondervinden, moeten een interactieve prompt weer geven, waarbij de gebruiker zich opnieuw moet aanmelden. Zie [Wat is er nieuw voor verificatie?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)voor meer informatie over deze wijziging en hoe u uw app kunt herstellen als deze fout optreedt.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nieuwe audit logboeken gebruikers ervaring nu beschikbaar

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

We hebben een nieuwe Azure AD- **controle logboek** pagina gemaakt waarmee u zowel de Lees baarheid als de manier waarop u naar uw gegevens zoekt, kunt verbeteren. Als u de nieuwe pagina **controle logboeken** wilt weer geven, selecteert u **controle logboeken** in het gedeelte **activiteit** van Azure AD.

![Nieuwe pagina controle logboeken, met voorbeeld gegevens](media/whats-new/audit-logs-page.png)

Zie voor meer informatie over de nieuwe pagina **controle logboeken** [activiteiten controleren in de Azure Active Directory Portal](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nieuwe waarschuwingen en richt lijnen om te voor komen dat onbedoelde beheerders niet-geconfigureerde beleids regels voor voorwaardelijke toegang kunnen worden vergrendeld

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Om te voor komen dat beheerders zichzelf via onjuist geconfigureerde beleids regels voor voorwaardelijke toegang per ongeluk kunnen vergren delen, hebben we nieuwe waarschuwingen en bijgewerkte richt lijnen gemaakt in de Azure Portal. Zie [Wat zijn service afhankelijkheden in azure Active Directory voorwaardelijke toegang](../conditional-access/service-dependencies.md)voor meer informatie over de nieuwe richt lijnen.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Betere gebruiks voorwaarden voor eind gebruikers op mobiele apparaten

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

We hebben onze bestaande gebruiks voorwaarden bijgewerkt om te helpen bij het verbeteren van de manier waarop u de gebruiks voorwaarden op een mobiel apparaat bekijkt en ermee akkoord gaat. U kunt nu in-en uitzoomen, terugkeren, de informatie downloaden en hyper links selecteren. Zie [Azure Active Directory functie gebruiksrecht overeenkomst](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users)voor meer informatie over de bijgewerkte gebruiks voorwaarden.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nieuwe ervaring voor het downloaden van Azure AD-activiteiten logboeken beschikbaar

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

U kunt nu grote hoeveel heden activiteiten logboeken rechtstreeks downloaden vanuit het Azure Portal. Met deze update kunt u het volgende doen:

- Down load Maxi maal 250.000 rijen.

- Ontvang een melding wanneer de down load is voltooid.

- Pas de bestands naam aan.

- Bepaal de indeling van de uitvoer, ofwel JSON of CSV.

Voor meer informatie over deze functie raadpleegt [u Quick Start: een controle rapport downloaden met behulp van de Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Belang rijke wijziging: updates voor evaluatie van voor waarden door Exchange ActiveSync (EAS)

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: Access Control

We zijn bezig met het bijwerken van de manier waarop Exchange ActiveSync (EAS) de volgende voor waarden evalueert:

- Gebruikers locatie, op basis van land, regio of IP-adres

- Aanmeldingsrisico

- Apparaatplatform

Als u deze voor waarden in uw beleid voor voorwaardelijke toegang eerder hebt gebruikt, is het mogelijk dat het probleem van de voor waarde wordt gewijzigd. Als u bijvoorbeeld eerder de voor waarde voor de gebruikers locatie in een beleid hebt gebruikt, kan het beleid worden overgeslagen op basis van de locatie van uw gebruiker.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configureerbaar Azure AD SAML-token versleuteling (open bare preview)

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

U kunt nu elke ondersteunde SAML-app configureren voor het ontvangen van versleutelde SAML-tokens. Wanneer Azure AD is geconfigureerd en gebruikt met een app, worden de verzonden SAML-bevestigingen versleuteld met behulp van een open bare sleutel die is verkregen van een certificaat dat is opgeslagen in azure AD.

Zie [Azure AD SAML-token versleuteling configureren](../manage-apps/howto-saml-token-encryption.md)voor meer informatie over het configureren van uw SAML-token versleuteling.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen of apps met behulp van Azure AD-toegangs beoordelingen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

U kunt nu meerdere groepen of apps opnemen in één Azure AD-toegangs beoordeling voor groepslid maatschap of app-toewijzing. Toegangs beoordelingen met meerdere groepen of apps worden ingesteld met dezelfde instellingen en alle opgenomen revisoren worden tegelijkertijd op de hoogte gesteld.

Zie [een toegangs beoordeling van groepen of toepassingen in azure AD-toegangs beoordelingen maken](../governance/create-access-review.md) voor meer informatie over hoe u een toegangs beoordeling maakt met behulp van Azure AD-toegangs beoordelingen

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-februari 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In februari 2019 zijn deze 27 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Euromonitor Pass Port](../saas-apps/euromonitor-passport-tutorial.md), [MINDTICKLE](../saas-apps/mindtickle-tutorial.md), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), Port [stack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [iDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), permission click, Brandfolder [,](../saas-apps/smartfile-tutorial.md)StoregateSmartFile [, Pexip,](../saas-apps/pexip-tutorial.md) [Stormboard](../saas-apps/stormboard-tutorial.md), [seismisch](../saas-apps/seismic-tutorial.md), [delen van een droom](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webmethodes integratie Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [kennis Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OE campus](../saas-apps/ou-campus-tutorial.md), [Peri Scope data](../saas-apps/periscope-data-tutorial.md), [NetOp Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud door Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), ClickUp [productiviteits platform](../saas-apps/clickup-productivity-platform-tutorial.md) [](../saas-apps/brandfolder-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Uitgebreide registratie van gecombineerde MFA/SSPR

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

Als reactie op feedback van klanten hebben we de preview-ervaring voor de gecombineerde MFA-SSPR-registratie verbeterd, waardoor uw gebruikers hun beveiligings gegevens voor zowel MFA als SSPR sneller kunnen registreren.

**Voer de volgende stappen uit om de verbeterde ervaring voor uw gebruikers vandaag in te scha kelen:**

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder en ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster te beheren**.

2. In de **gebruikers die de preview-functies voor het registreren en beheren van beveiligings gegevens – optie voor het vernieuwen kunnen gebruiken** , kiest u de functies voor een **geselecteerde groep gebruikers** of voor **alle gebruikers**.

In de komende weken verwijderen we de mogelijkheid om de oude gecombineerde registratie Preview voor MFA/SSPR voor tenants die nog niet zijn ingeschakeld, in te scha kelen.

**Voer de volgende stappen uit om te controleren of het besturings element wordt verwijderd voor uw Tenant:**

1. Meld u aan bij de Azure Portal als globale beheerder of gebruikers beheerder en ga naar **Azure Active Directory > gebruikers instellingen > instellingen voor de preview-functies van het toegangs venster te beheren**.

2. Als de **gebruikers die de optie preview-functies voor het registreren en beheren van beveiligings gegevens hebben** , zijn ingesteld op **geen**, wordt de optie verwijderd uit uw Tenant.

Ongeacht of u eerder de oude gecombineerde MFA/SSPR registratie preview-ervaring voor gebruikers hebt ingeschakeld, wordt de oude ervaring op een later tijdstip uitgeschakeld. Daarom wordt u zo snel mogelijk geadviseerd om over te stappen op de nieuwe, verbeterde ervaring.

Voor meer informatie over de verbeterde registratie-ervaring raadpleegt [u de leuke verbeteringen van de Azure AD gecombineerde MFA-en registratie-ervaring voor het opnieuw instellen van wacht woorden](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Beleids beheer ervaring voor gebruikers stromen bijgewerkt

**Type:** Gewijzigde functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

We hebben het proces voor het maken en beheren van beleid voor gebruikers stromen bijgewerkt (voorheen bekend als ingebouwde beleids regels). Deze nieuwe ervaring is nu de standaard instelling voor al uw Azure AD-tenants.

U kunt aanvullende feedback en suggesties bieden met behulp van de pictogrammen glim lach of frons in het gebied **Feedback verzenden** boven aan het portal scherm.

Voor meer informatie over de nieuwe beleids beheer-ervaring raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Specifieke versie van pagina-elementen kiezen die wordt opgegeven door Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu een specifieke versie van de pagina-elementen kiezen die door Azure AD B2C worden verschaft. Door een specifieke versie te selecteren, kunt u de updates testen voordat ze op een pagina worden weer gegeven en kunt u voorspel bare gedrag ophalen. Daarnaast kunt u ervoor kiezen om specifieke pagina versies af te dwingen om Java script-aanpassingen toe te staan. Als u deze functie wilt inschakelen, gaat u naar de pagina **Eigenschappen** in uw gebruikers stromen.

Voor meer informatie over het kiezen van specifieke versies van pagina-elementen raadpleegt u de [Azure AD B2C nu java script customization en veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Configureer bare vereisten voor het wacht woord voor de eind gebruiker voor B2C (GA)

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu de wachtwoord complexiteit van uw organisatie instellen voor uw eind gebruikers, in plaats van uw systeem eigen Azure AD-wachtwoord beleid te gebruiken. Op de Blade **Eigenschappen** van uw gebruikers stromen (voorheen bekend als uw ingebouwde beleids regels) kunt u een wachtwoord complexiteit van **eenvoudig** of **sterk** kiezen, of u kunt een **aangepaste** set vereisten maken.

Zie [complexiteits vereisten configureren voor wacht woorden in azure Active Directory B2C](../../active-directory-b2c/password-complexity.md)voor meer informatie over de configuratie van de vereisten voor wachtwoord complexiteit.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nieuwe standaard sjablonen voor aangepaste merk authenticatie-ervaringen

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt onze nieuwe standaard sjablonen op de Blade **pagina-indelingen** van uw gebruikers stromen (voorheen bekend als ingebouwde beleids regels) gebruiken om een aangepaste merk bare verificatie-ervaring voor uw gebruikers te maken.

Voor meer informatie over het gebruik van de sjablonen raadpleegt u [Azure AD B2C nu java script-aanpassing en nog veel meer nieuwe functies](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samen werking met authenticatie met eenmalige verificatie (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

We hebben eenmalige wachtwoord verificatie (OTP) geïntroduceerd voor B2B-gast gebruikers die niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Deze nieuwe verificatie methode betekent dat gast gebruikers geen nieuwe Microsoft-account hoeven te maken. Een gast gebruiker kan in plaats daarvan een uitnodiging inwisselen of toegang tot een gedeelde bron aanvragen om een tijdelijke code aan een e-mail adres te verzenden. Met deze tijdelijke code kan de gast gebruiker zich blijven aanmelden.

Zie voor meer informatie [e-mail One-time wachtwoord code verificatie (preview)](../external-identities/one-time-passcode.md) en de blog. [Azure AD maakt delen en samen werking naadloos mogelijk voor elke gebruiker met een account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nieuwe instellingen voor Azure AD-toepassingsproxy cookie

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

We hebben drie nieuwe cookie-instellingen geïntroduceerd, die beschikbaar zijn voor uw apps die zijn gepubliceerd via toepassings proxy:

- **Gebruik HTTP-Only cookie.** Hiermee stelt u de **HTTPOnly** -vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen, zoals het voor komen van het kopiëren of wijzigen van cookies via scripting aan de client zijde. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Gebruik beveiligde cookie.** Hiermee stelt u de **beveiligde** vlag in voor de toegangs-en sessie cookies van de toepassings proxy. Het inschakelen van deze instelling biedt extra veiligheids voordelen door ervoor te zorgen dat cookies alleen worden verzonden via TLS-beveiligde kanalen, zoals HTTPS. U wordt aangeraden deze vlag in te scha kelen ( **Ja**) om de extra voor delen te bepalen.

- **Permanente cookie gebruiken.** Hiermee voor komt u dat de toegang tot cookies verloopt wanneer de webbrowser is gesloten. Deze cookies duren voor de levens duur van het toegangs token. De cookies worden echter opnieuw ingesteld als de verloop tijd wordt bereikt of als de gebruiker de cookie hand matig verwijdert. We raden u aan om de standaard instelling **Nee** te laten, alleen de instelling in te scha kelen voor oudere apps die geen cookies delen tussen processen.

Zie [cookie-instellingen voor toegang tot on-premises toepassingen in azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md)voor meer informatie over de nieuwe cookies.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-januari 2019

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In januari 2019 hebben we deze 35 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [talen palet](../saas-apps/talent-palette-tutorial.md) [infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco paraplu](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [verloop herinnering](../saas-apps/expiration-reminder-tutorial.md), InstaVR- [Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [werk woord](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud service Picco](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), belopend, [CallPlease](https://webapp.callplease.com/create-account/create-account.html), GTNEXUS- [SSO-systeem](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md) [, Signagelive,](../saas-apps/signagelive-tutorial.md) [aren voor Enter prise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 voor Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [bezoek](../saas-apps/visitly-tutorial.md), [Korn veer Alp](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [](../saas-apps/workable-tutorial.md) [Adoddle cSaas](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nieuwe Azure AD Identity Protection verbeteringen (open bare preview)

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

We zijn enthousiast dat we de volgende uitbrei dingen hebben toegevoegd aan de aanbieding van Azure AD Identity Protection open bare preview, waaronder:

- Een bijgewerkte en meer geïntegreerde gebruikers interface

- Aanvullende Api's

- Verbeterde risico analyse via machine learning

- Uitlijning op het hele product voor Risk ante gebruikers en Risk ante aanmeldingen

Zie [Wat is Azure Active Directory Identity Protection (vernieuwd)?](../identity-protection/overview-identity-protection.md) voor meer informatie over de uitbrei dingen. voor meer informatie en om je mening te delen via de prompts in het product.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nieuwe app-vergrendelings functie voor de app Microsoft Authenticator op iOS-en Android-apparaten

**Type:** Nieuwe functie **service categorie:** Microsoft Authenticator app- **product mogelijkheid:** identiteits beveiliging & beveiliging

Als u uw eenmalige wachtwoord code, app-informatie en app-instellingen veiliger wilt laten, kunt u de functie voor het vergren delen van apps inschakelen in de app Microsoft Authenticator. Wanneer u app Lock inschakelt, wordt u gevraagd om te verifiëren met uw pincode of biometrisch telkens wanneer u de Microsoft Authenticator-app opent.

Zie de [Veelgestelde vragen over de Microsoft Authenticator-app](../user-help/user-help-auth-app-faq.md)voor meer informatie.

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Export mogelijkheden voor verbeterde Azure AD Privileged Identity Management (PIM)

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Privileged Identity Management (PIM) beheerders kunnen nu alle actieve en in aanmerking komende roltoewijzingen voor een specifieke resource exporteren, die roltoewijzingen voor alle onderliggende resources bevat. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement te krijgen en ze moest roltoewijzingen voor elke specifieke resource exporteren.

Zie [activiteit en controle geschiedenis voor Azure-resource rollen weer geven in PIM](../privileged-identity-management/azure-pim-resource-rbac.md)voor meer informatie.

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gebruikers die zijn verwijderd uit het synchronisatie bereik, worden niet meer overgeschakeld naar alleen-Cloud accounts

**Type:** Vaste **service categorie:** gebruikers beheer **product mogelijkheid:** map

>[!Important]
>We hebben uw frustratie gehoord en begrepen door deze oplossing. Daarom hebben we deze wijziging tot nu toe gewijzigd, zodat u de oplossing gemakkelijker kunt implementeren in uw organisatie.

Er is een fout opgelost waarbij de vlag DirSyncEnabled van een gebruiker onjuist wordt overgeschakeld naar **False** wanneer het object Active Directory Domain Services (AD DS) is uitgesloten van het synchronisatie bereik en vervolgens is verplaatst naar de Prullenbak in azure ad tijdens de volgende synchronisatie cyclus. Als gevolg van deze oplossing geldt dat als de gebruiker is uitgesloten van het synchronisatie bereik en daarna wordt hersteld vanuit de Prullenbak van Azure AD, het gebruikers account blijft gesynchroniseerd van on-premises AD, zoals verwacht, en niet kan worden beheerd in de Cloud omdat de bron van de autoriteit (SoA) blijft als on-premises AD.

Voorafgaand aan deze oplossing is er een probleem opgetreden toen de vlag DirSyncEnabled is overgeschakeld naar onwaar. Er is een onjuiste indruk gegeven dat deze accounts zijn geconverteerd naar objecten in de Cloud en dat de accounts in de Cloud kunnen worden beheerd. De accounts behouden echter nog steeds hun SoA als on-premises en alle gesynchroniseerde eigenschappen (schaduw kenmerken) die afkomstig zijn van on-premises AD. Deze voor waarde heeft meerdere problemen ondervonden in azure AD en andere Cloud werkbelastingen (zoals Exchange Online) die verwacht worden deze accounts te behandelen als gesynchroniseerd vanuit AD, maar nu fungeren als alleen Cloud accounts.

Op dit moment is de enige manier om een gesynchroniseerd-van-AD-account echt te converteren naar een alleen-Cloud account door DirSync op Tenant niveau uit te scha kelen, waarmee een back-end-bewerking wordt geactiveerd om de SoA over te dragen. Dit type SoA-wijziging vereist (maar is niet beperkt tot) het schoonmaken van alle on-premises verwante kenmerken (zoals LastDirSyncTime en schaduw kenmerken) en het verzenden van een signaal naar andere Cloud werkbelastingen zodat het desbetreffende object ook naar een alleen-Cloud account wordt geconverteerd.

Deze oplossing verhindert daarom directe updates van het kenmerk ImmutableID van een gebruiker die is gesynchroniseerd vanuit AD, wat in sommige scenario's in het verleden was vereist. Het ImmutableID van een object in azure AD, zoals de naam al aangeeft, is bedoeld om onveranderbaar te zijn. Nieuwe functies die in Azure AD Connect Health zijn geïmplementeerd en Azure AD Connect-synchronisatie-client, zijn beschikbaar om deze scenario's te verhelpen:

- **Grootschalige ImmutableID-update voor veel gebruikers in een gefaseerde benadering**

  U moet bijvoorbeeld een langdurige AD DS tussen forest-migratie uitvoeren. Oplossing: gebruik Azure AD Connect om het **bron anker te configureren** en kopieer, wanneer de gebruiker migreert, de bestaande ImmutableID-waarden van Azure AD naar het kenmerk MS-DS-Consistency-GUID van de lokale AD DS gebruiker van het nieuwe forest. Zie [using MS-DS-ConsistencyGuid als source Anchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)voor meer informatie.

- **Grootschalige ImmutableID-updates voor veel gebruikers per foto**

  Tijdens de implementatie Azure AD Connect u bijvoorbeeld een fout gemaakt en nu moet u het kenmerk source Anchor wijzigen. Oplossing: Schakel DirSync uit op Tenant niveau en wis alle ongeldige ImmutableID-waarden. Zie Directory-synchronisatie uitschakelen [voor Office 365](/office365/enterprise/turn-off-directory-synchronization)voor meer informatie.

- **Een on-premises gebruiker opnieuw koppelen aan een bestaande gebruiker in azure AD** Een gebruiker die opnieuw is gemaakt in AD DS genereert bijvoorbeeld een duplicaat in het Azure AD-account in plaats van het opnieuw te koppelen aan een bestaand Azure AD-account (zwevend object). Oplossing: gebruik Azure AD Connect Health in de Azure Portal om de bron-anker-ImmutableID opnieuw toe te wijzen. Zie voor meer informatie [zwevend object scenario](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Belang rijke wijziging: updates voor het schema voor controle en aanmeldings logboeken via Azure Monitor

**Type:** Gewijzigde functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Momenteel publiceren we de audit-en aanmeldings logboek stromen via Azure Monitor, zodat u de logboek bestanden naadloos kunt integreren met uw SIEM-hulpprogram ma's of met Log Analytics. Op basis van uw feedback en in de voor bereiding van de algemene Beschik baarheid van deze functie, worden de volgende wijzigingen aangebracht in het schema. Deze schema wijzigingen en de bijbehorende documentatie-updates worden uitgevoerd door de eerste week van januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nieuwe velden in het audit schema
Er wordt een nieuw **bewerkings type** veld toegevoegd om het type bewerking te bieden dat wordt uitgevoerd op de resource. Bijvoorbeeld: **toevoegen**, **bijwerken** of **verwijderen**.

#### <a name="changed-fields-in-the-audit-schema"></a>Gewijzigde velden in het audit schema
De volgende velden worden gewijzigd in het controle schema:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|Categorie|Dit is het veld **service naam** . Nu is het veld **controle categorieën** . De naam van de **service naam** is gewijzigd in het veld **loggedByService** .|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wacht woord opnieuw instellen</li></ul>|<ul><li>Gebruikersbeheer</li><li>Groepsbeheer</li><li>App-beheer</li></ul>|
|targetResources|Bevat **TargetResourceType** op het hoogste niveau.|&nbsp;|<ul><li>Beleid</li><li>App</li><li>Gebruiker</li><li>Groep</li></ul>|
|loggedByService|Geeft de naam van de service die het audit logboek heeft gegenereerd.|Null|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wachtwoord opnieuw instellen</li></ul>|
|Resultaat|Levert het resultaat van de audit Logboeken. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>Fout</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Gewijzigde velden in het aanmeldings schema
De volgende velden worden gewijzigd in het aanmeldings schema:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Dit is het **conditionalaccessPolicies** -veld. Nu is het veld **appliedConditionalAccessPolicies** .|Geen wijziging|Geen wijziging|
|conditionalAccessStatus|Hiermee wordt het resultaat van de status van het beleid voor voorwaardelijke toegang weer geven bij het aanmelden. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|
|appliedConditionalAccessPolicies: resultaat|Hiermee wordt het resultaat van de individuele status van het beleid voor voorwaardelijke toegang weer geven bij het aanmelden. Voorheen werd dit opgesomd, maar de werkelijke waarde wordt nu weer gegeven.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|

Zie [het schema voor Azure AD-controle logboeken interpreteren in azure monitor (preview)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md) voor meer informatie over het schema.

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Verbeteringen op het gebied van identiteits beveiliging in het machine learning model met toezicht en de risico Score-engine

**Type:** Gewijzigde functie **service categorie:** identiteits beschermings **product:** risico scores

Verbeteringen aan de gebruikers-en aanmeldings risico beoordelings engine voor identiteits beveiliging kunnen helpen de nauw keurigheid en dekking van de gebruikers Risico's te verbeteren. Beheerders kunnen merken dat het risico niveau van de gebruiker niet langer rechtstreeks is gekoppeld aan het risico niveau van specifieke detecties en dat er een toename is in het aantal en het niveau van Risk ante aanmeldings gebeurtenissen.

Risico detecties worden nu geëvalueerd door het machine learning model met toezicht, waarmee gebruikers Risico's worden berekend met behulp van aanvullende functies van de aanmeldingen van de gebruiker en een patroon van detecties. Op basis van dit model kan de beheerder gebruikers met hoge risico scores vinden, zelfs als detecties die aan die gebruiker zijn gekoppeld, een laag of gemiddeld risico hebben.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Beheerders kunnen hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator-app (open bare preview)

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

Azure AD-beheerders kunnen nu hun eigen wacht woord opnieuw instellen met behulp van de Microsoft Authenticator app-meldingen of een code van een mobiele verificator-app of-hardware-token. Beheerders kunnen nu twee van de volgende methoden gebruiken om hun eigen wacht woord opnieuw in te stellen:

- App-melding Microsoft Authenticator

- Andere Mobile Authenticator-app/hardware-token code

- E-mail

- Telefoongesprek

- Sms-bericht

Voor meer informatie over het gebruik van de Microsoft Authenticator-app om wacht woorden opnieuw in te stellen, Zie [selfservice voor wachtwoord herstel van Azure AD-mobiele app en SSPR (preview-versie)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nieuwe rol van Azure AD-Cloud apparaat-beheerder (open bare preview)

**Type:** Nieuwe functie **service categorie:** de mogelijkheid om het apparaat te registreren en te beheren **:** toegangs beheer

Beheerders kunnen gebruikers toewijzen aan de nieuwe rol van de beheerder van het Cloud apparaat voor het uitvoeren van beheer taken voor de Cloud. Gebruikers met de rol Administrators van Cloud apparaten kunnen apparaten in azure AD inschakelen, uitschakelen en verwijderen, en kunnen Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal lezen.

Zie [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md) voor meer informatie over rollen en machtigingen.

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Uw apparaten beheren met de nieuwe tijds tempel van de activiteit in azure AD (open bare preview)

**Type:** Nieuwe functie **service categorie:** apparaat registratie en beheer **product mogelijkheid:** levenscyclus beheer van apparaten

Na verloop van tijd moet u de apparaten van uw organisatie in azure AD vernieuwen en buiten gebruik stellen om te voor komen dat er verouderde apparaten in uw omgeving worden uitgevoerd. Om u te helpen bij dit proces, worden uw apparaten nu door Azure AD bijgewerkt met een nieuwe tijds tempel van de activiteit, zodat u de levens cyclus van uw apparaat kunt beheren.

Zie [How to: de verouderde apparaten beheren in azure AD](../devices/manage-stale-devices.md) voor meer informatie over het ophalen en gebruiken van deze tijds tempel.

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Beheerders kunnen vereisen dat gebruikers de gebruiks voorwaarden voor elk apparaat accepteren

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Beheerders kunnen nu de optie **gebruikers moeten toestemming geven op elk apparaat** , zodat uw gebruikers de gebruiks voorwaarden kunnen accepteren op elk apparaat dat ze gebruiken in uw Tenant.

Zie de [sectie met gebruiks voorwaarden per apparaat van de functie Azure Active Directory gebruiksrecht overeenkomst](../conditional-access/terms-of-use.md#per-device-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van een terugkerend schema

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance


Beheerders kunnen de optie voor het **verstrijken van verlopen** nu inschakelen om een gebruiks voorwaarden te laten verlopen voor al uw gebruikers op basis van uw opgegeven terugkerende planning. De planning kan jaarlijks, tweejaarlijkse, elk kwar taal of maandelijks zijn. Nadat de gebruiks voorwaarden verlopen zijn, moeten gebruikers deze opnieuw accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](../conditional-access/terms-of-use.md#add-terms-of-use)voor meer informatie.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Beheerders kunnen een gebruiks voorwaarden configureren om te verlopen op basis van de planning van elke gebruiker

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Beheerders kunnen nu een duur opgeven die de gebruiker nodig heeft om een gebruiks voorwaarden opnieuw te accepteren. Beheerders kunnen bijvoorbeeld opgeven dat gebruikers elke 90 dagen een gebruiks voorwaarden opnieuw moeten accepteren.

Zie de [sectie gebruiks voorwaarden toevoegen van de functie Azure Active Directory gebruiksrecht overeenkomst](../conditional-access/terms-of-use.md#add-terms-of-use)voor meer informatie.

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nieuwe Azure AD Privileged Identity Management (PIM) e-mails voor Azure Active Directory rollen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Klanten die Azure AD Privileged Identity Management (PIM) gebruiken, kunnen nu een wekelijks overzicht van e-mail ontvangen, met inbegrip van de volgende informatie over de afgelopen zeven dagen:

- Overzicht van de meest geschikte en permanente roltoewijzingen

- Aantal gebruikers die rollen activeren

- Aantal gebruikers dat is toegewezen aan rollen in PIM

- Aantal gebruikers dat is toegewezen aan rollen buiten PIM

- Het aantal gebruikers dat permanent is gemaakt in PIM

Zie [e-mail meldingen in PIM](../privileged-identity-management/pim-email-notifications.md)voor meer informatie over PIM en de beschik bare e-mail meldingen.

---

### <a name="group-based-licensing-is-now-generally-available"></a>Op groep gebaseerde licentie verlening is nu algemeen beschikbaar

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Directory

De open bare preview-versie van een groeps licentie is niet beschikbaar en is nu algemeen verkrijgbaar. Als onderdeel van deze algemene release hebben we deze functie uitgebreidere schaalbaar en hebben ze de mogelijkheid voor het opnieuw verwerken van op groepen gebaseerde licentie toewijzingen voor één gebruiker en de mogelijkheid om op groep gebaseerde licentie verlening te gebruiken met Office 365 E3/a3-licenties.

Zie [Wat is op groep gebaseerde licentie verlening in azure Active Directory](./active-directory-licensing-whatis-azure-portal.md) voor meer informatie over groeps licenties?

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-november 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In november 2018 hebben deze 26 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [GRA-PE](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite campus](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [drift](../saas-apps/drift-tutorial.md), [Zenegy voor Business Central 365](https://accounting.zenegy.com/), [Everbridge member Portal](../saas-apps/everbridge-tutorial.md), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound](../saas-apps/allbound-sso-tutorial.md), [Plex apps-klassieke test](https://test.plexonline.com/signon), [Plex-apps-klassiek](https://www.plexonline.com/signon), Plex [-apps-UX testen](https://test.cloud.plex.com/sso), [Plex apps – UX](https://cloud.plex.com/sso), [Plex apps – iam](https://accounts.plex.com/), [Craft-Childcare records, aanwezigheid, & systeem voor financiële registratie](https://getcrafts.ca/craftsregistration)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-logboeken werken nu met Azure Log Analytics (open bare preview)

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Het is enthousiast om te melden dat u uw Azure AD-logboeken nu kunt door sturen naar Azure Log Analytics! Deze belangrijkste functie biedt u nog betere toegang tot analyses voor uw bedrijf, bedrijfs activiteiten en beveiliging, en een manier om uw infra structuur te bewaken. Zie voor meer informatie de [Azure Active Directory activiteiten Logboeken in Azure log Analytics nu beschikbaar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-oktober 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In oktober 2018 hebben we deze 14 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Mijn toekennings punten](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler drie](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [beoordeeld](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mail meldingen Azure AD Domain Services

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services **Product capaciteit:** Azure AD Domain Services

Azure AD Domain Services biedt waarschuwingen over de Azure Portal over onjuiste configuratie of problemen met uw beheerde domein. Deze waarschuwingen bevatten stapsgewijze hand leidingen, zodat u kunt proberen de problemen op te lossen zonder dat u contact hoeft op te nemen met de ondersteuning.

Vanaf oktober kunt u de instellingen voor meldingen voor uw beheerde domein aanpassen zodat er nieuwe waarschuwingen optreden, wordt een e-mail bericht verzonden naar een aangewezen groep personen, waardoor de portal voortdurend niet meer hoeft te worden gecontroleerd op updates.

Zie [meldings instellingen in azure AD Domain Services](../../active-directory-domain-services/notifications.md)voor meer informatie.

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD Portal biedt ondersteuning voor het gebruik van de ForceDelete-domein-API voor het verwijderen van aangepaste domeinen

**Type:** Gewijzigde functie **service categorie:** Directory Management **product Capability:** Directory

Het is blij dat u de ForceDelete-domein-API kunt gebruiken om uw aangepaste domein namen te verwijderen door de naam van verwijzingen, zoals gebruikers, groepen en apps van uw aangepaste domein naam (contoso.com), weer te wijzigen in de oorspronkelijke standaard domeinnaam (contoso.onmicrosoft.com).

Met deze wijziging kunt u uw aangepaste domein namen sneller verwijderen als uw organisatie de naam niet meer gebruikt of als u de domein naam moet gebruiken met een andere Azure AD.

Zie [een aangepaste domein naam verwijderen](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)voor meer informatie.

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>De machtigingen voor de beheerdersrol voor dynamische groepen zijn bijgewerkt

**Type:** Vaste **service categorie:** functionaliteit voor groeps beheer **product:** samen werking

Er is een probleem opgelost waardoor specifieke beheerders rollen nu dynamische lidmaatschaps regels kunnen maken en bijwerken, zonder dat de eigenaar van de groep hoeft te zijn.

De rollen zijn:

- Globale beheerder

- Intune-beheerder

- Gebruikersbeheerder

Zie [een dynamische groep maken en de status controleren](../enterprise-users/groups-create-rule.md) voor meer informatie

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Vereenvoudigde configuratie-instellingen voor eenmalige Sign-On (SSO) voor sommige apps van derden

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We realiseren dat het instellen van een enkele Sign-On (SSO) voor SaaS-apps (Software as a Service) lastig kan zijn vanwege de unieke aard van elke configuratie van de apps. We hebben een vereenvoudigde configuratie-ervaring ontwikkeld voor het automatisch invullen van de SSO-configuratie-instellingen voor de volgende SaaS-apps van derden:

- Zendesk

- ArcGis online

- Jamf Pro

Ga naar de   >  **configuratie** pagina van Azure Portal SSO voor de app om te beginnen met het gebruik van deze sessie met één klik. Zie voor meer informatie [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory-waar bevinden zich uw gegevens? Faxvoorblad

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** GoLocal

Selecteer de regio van uw bedrijf in de **Azure Active Directory-waar** bevindt zich uw gegevens pagina om te zien welk Azure-Data Center uw Azure AD-gegevens in rust heeft voor alle Azure AD-Services. U kunt de informatie filteren op specifieke Azure AD-Services voor de regio van uw bedrijf.

Als u toegang wilt krijgen tot deze functie en voor meer informatie, Zie [Azure Active Directory-waar bevindt zich uw gegevens](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nieuw implementatie plan beschikbaar voor het toegangs venster mijn apps

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

Bekijk het nieuwe implementatie plan dat beschikbaar is voor het toegangs venster mijn apps ( https://aka.ms/deploymentplans) .
Het toegangs venster voor mijn apps biedt gebruikers één plek om hun apps te vinden en toegang te krijgen. Deze portal biedt gebruikers ook selfservice mogelijkheden, zoals het aanvragen van toegang tot apps en groepen, of het beheren van toegang tot deze bronnen namens anderen.

Zie [Wat is de portal mijn apps?](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nieuwe tabblad probleem oplossing en ondersteuning op de pagina aanmeld logboeken van de Azure Portal

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Het nieuwe tabblad **probleem oplossing en ondersteuning** op de pagina **aanmeldingen** van het Azure Portal, is bedoeld om beheerders te helpen bij het oplossen van problemen met betrekking tot Azure AD-aanmeldingen. Dit nieuwe tabblad bevat de fout code, het fout bericht en aanbevelingen voor herstel (indien van toepassing) om het probleem op te lossen. Als u het probleem niet kunt oplossen, geeft u ook een nieuwe manier om een ondersteunings ticket te maken met behulp van de functie **kopiëren naar klem bord** , waarmee de velden **aanvraag-id** en **datum (UTC)** voor het logboek bestand in uw ondersteunings ticket worden ingevuld.

![Aanmeld logboeken met het nieuwe tabblad](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Verbeterde ondersteuning voor aangepaste extensie-eigenschappen die worden gebruikt voor het maken van dynamische lidmaatschaps regels

**Type:** Gewijzigde functie **service categorie:** groeps beheer **product capaciteit:** samen werking

Met deze update kunt u nu klikken op de koppeling **aangepaste extensie eigenschappen ophalen** van de opbouw functie voor de groep voor dynamische gebruikers, uw unieke App-ID invoeren en de volledige lijst met aangepaste extensie-eigenschappen ontvangen die moeten worden gebruikt bij het maken van een dynamische lidmaatschaps regel voor gebruikers. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app op te halen.

Voor meer informatie over het gebruik van aangepaste uitbreidings eigenschappen voor dynamische lidmaatschaps regels raadpleegt u de eigenschappen van de [extensie en aangepaste extensie](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft To-Do

- Microsoft Stream

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nieuwe ondersteuning voor het opnieuw instellen van Self-Service wacht woord vanaf het vergrendelings scherm van Windows 7/8/8.1

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Nadat u deze nieuwe functie hebt ingesteld, krijgen gebruikers een koppeling te zien om hun wacht woord opnieuw in te stellen vanaf het **vergrendelings** scherm van een apparaat met Windows 7, Windows 8 of Windows 8,1. Als u op deze koppeling klikt, wordt de gebruiker via de webbrowser begeleid via dezelfde wachtwoord herstel stroom.

Zie [How to enable password reset from Windows 7, 8 en 8,1 (](../authentication/howto-sspr-windows.md) Engelstalig) voor meer informatie.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigings bericht: autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Type:** Planning voor **service categorie wijzigen:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf 15 november 2018 zal Azure AD stoppen met het accepteren van eerder gebruikte verificatie codes voor apps. Deze beveiligings wijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1-als v2-eind punten.

Als uw app autorisatie codes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan om de code te gebruiken om een vernieuwings token op te halen en vervolgens dat vernieuwings token te gebruiken voor het verkrijgen van aanvullende tokens voor andere resources. Autorisatie codes kunnen slechts één keer worden gebruikt, maar vernieuwings tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom, krijgt een invalid_grant fout.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-september 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In september 2018 hebben we deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Uberflip](../saas-apps/uberflip-tutorial.md), [tegemoetkomen aan wervings software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enter prise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [sneeuw](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [racks Pace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO voor Azure, surveymonkey, [bijeenroeping](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ondersteuning voor aanvullende methoden voor claim transformaties

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

We hebben nieuwe claim transformatie methoden, ToLower () en ToUpper () geïntroduceerd, die kunnen worden toegepast op SAML-tokens van de op SAML gebaseerde configuratie pagina voor **één Sign-On** .

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure AD](../develop/active-directory-saml-claims-customization.md) voor meer informatie.

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Bijgewerkte gebruikers interface voor app-configuratie op basis van SAML (preview-versie)

**Type:** Gewijzigde functie **service categorie:** Enter prise apps **product Capability:** SSO

Als onderdeel van onze bijgewerkte app configuratie-UI op basis van SAML krijgt u het volgende:

- Een bijgewerkte walkthrough-ervaring voor het configureren van uw op SAML gebaseerde apps.

- Meer inzicht in wat er ontbreekt of onjuist is in uw configuratie.

- De mogelijkheid om meerdere e-mail adressen toe te voegen voor de melding van een verlopen certificaat.

- Nieuwe claim transformatie methoden, ToLower () en ToUpper (), en meer.

- Een manier om uw eigen token handtekening certificaat voor uw zakelijke apps te uploaden.

- Een manier om de NameID-indeling voor SAML-apps in te stellen en een manier om de NameID-waarde als Directory-extensies in te stellen.

Als u deze bijgewerkte weer gave wilt inschakelen, klikt u op de koppeling **onze nieuwe ervaring proberen** vanaf de bovenkant van de pagina voor **eenmalige aanmelding** . Zie [zelf studie: eenmalige aanmelding op basis van SAML configureren voor een toepassing met Azure Active Directory](../manage-apps/view-applications-portal.md)voor meer informatie.

---

## <a name="august-2018"></a>Augustus 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Wijzigingen in Azure Active Directory IP-adresbereiken

**Type:** Planning voor **service categorie wijzigen:** andere **product capaciteit:** platform

We introduceren een groter IP-bereik voor Azure AD. Dit betekent dat als u Azure AD IP-adresbereiken voor uw firewalls, routers of netwerk beveiligings groepen hebt geconfigureerd, deze moeten worden bijgewerkt. We maken deze update, zodat u de IP-adresbereiken van uw firewall, router of netwerk beveiligings groepen niet opnieuw hoeft te wijzigen wanneer Azure AD nieuwe eind punten toevoegt.

Netwerk verkeer wordt in de volgende twee maanden verplaatst naar deze nieuwe bereiken. Als u wilt door gaan met een ononderbroken service, moet u deze bijgewerkte waarden vóór 10 september 2018 toevoegen aan uw IP-adressen:

- 20.190.128.0/18

- 40.126.0.0/18

Het is raadzaam om de oude IP-adresbereiken pas te verwijderen als al uw netwerk verkeer is verplaatst naar de nieuwe bereiken. Zie [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken voor updates over de verplaatsing en om te leren wanneer u de oude bereiken kunt verwijderen.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigings bericht: autorisatie codes kunnen niet langer opnieuw worden gebruikt

**Type:** Planning voor **service categorie wijzigen:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Vanaf 15 november 2018 zal Azure AD stoppen met het accepteren van eerder gebruikte verificatie codes voor apps. Deze beveiligings wijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1-als v2-eind punten.

Als uw app autorisatie codes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan om de code te gebruiken om een vernieuwings token op te halen en vervolgens dat vernieuwings token te gebruiken voor het verkrijgen van aanvullende tokens voor andere resources. Autorisatie codes kunnen slechts één keer worden gebruikt, maar vernieuwings tokens kunnen meerdere keren worden gebruikt in meerdere resources. Een app die probeert een verificatie code opnieuw te gebruiken tijdens de OAuth-code stroom, krijgt een invalid_grant fout.

Zie voor deze en andere protocollen gerelateerde wijzigingen [de volledige lijst met nieuwe functies voor verificatie](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Geconvergeerde beveiligings gegevens beheren voor SSPR (self-service password) en Multi-Factor Authentication (MFA)

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Deze nieuwe functie helpt gebruikers bij het beheren van hun beveiligings gegevens (zoals, telefoon nummer, mobiele app, enzovoort) voor SSPR en MFA op één locatie en ervaring; in vergelijking met eerder, waar de oplossing is uitgevoerd op twee verschillende locaties.

Deze geconvergeerde ervaring werkt ook voor mensen die gebruikmaken van SSPR of MFA. Als uw organisatie geen MFA-of SSPR-registratie afdwingt, kunnen gebruikers nog steeds de door uw organisatie toegestane MFA-of SSPR Security-gegevens methoden registreren vanuit de portal mijn apps.

Dit is een open bare preview-versie. Beheerders kunnen de nieuwe ervaring (indien gewenst) voor een geselecteerde groep of voor alle gebruikers in een Tenant inschakelen. Voor meer informatie over de geconvergeerde ervaring raadpleegt u de [geconvergeerde ervaring blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Instelling voor nieuwe HTTP-Only cookies in azure AD Application proxy-apps

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Er is een nieuwe instelling met de naam **http-only cookies** in uw toepassings proxy-apps. Deze instelling helpt extra beveiliging te bieden door de HTTPOnly-vlag op te nemen in de HTTP-reactie header voor toegang tot de toepassings proxy en sessie cookies, de toegang tot de cookie vanuit een script aan de client zijde te stoppen en acties te voor komen zoals het kopiëren of aanpassen van de cookie. Hoewel deze vlag niet eerder is gebruikt, zijn uw cookies altijd versleuteld en verzonden met behulp van een TLS-verbinding om te helpen beschermen tegen onjuiste wijzigingen.

Deze instelling is niet compatibel met apps die gebruikmaken van ActiveX-besturings elementen, zoals Extern bureaublad. Als u in deze situatie bent, raden we u aan deze instelling uit te scha kelen.

Zie [toepassingen publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-add-on-premises-application.md)voor meer informatie over de instelling voor het HTTP-Only cookies.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) voor Azure-resources ondersteunt resource typen van beheer groepen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Just-in-time-activering en toewijzings instellingen kunnen nu worden toegepast op resource typen van de beheer groep, net zoals u al hebt gedaan voor abonnementen, resource groepen en resources (zoals Vm's, App Services en meer). Daarnaast kan iedereen met een rol die beheerders toegang biedt voor een beheer groep die resource in PIM detecteren en beheren.

Zie [Azure-resources detecteren en beheren met privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md) voor meer informatie over PIM-en Azure-resources.

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Toegang tot toepassingen (preview) biedt snellere toegang tot de Azure AD-Portal

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Wanneer een rol wordt geactiveerd met behulp van PIM, kan het langer dan tien minuten duren voordat de machtigingen van kracht worden. Als u ervoor kiest om toegang tot de toepassing te gebruiken, die momenteel beschikbaar is als open bare preview, hebben beheerders toegang tot de Azure AD-Portal zodra de activerings aanvraag is voltooid.

Momenteel ondersteunt toepassings toegang alleen de Azure AD Portal-ervaring en Azure-resources. Zie [Wat is Azure AD privileged Identity Management?](../privileged-identity-management/pim-configure.md) voor meer informatie over PIM en toegang tot toepassingen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-augustus 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2018 augustus hebben we deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Hornbill](../saas-apps/hornbill-tutorial.md), [BridgeLine](../saas-apps/bridgelineunbound-tutorial.md)niet-gebonden [, saus Labs-mobiele en webtests](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [META netwerken connector](../saas-apps/metanetworksconnector-tutorial.md), [manier van werken](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [Promaster (per Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [dossier](../saas-apps/dossier-tutorial.md), [N2F-onkosten rapporten](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 live chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Ondersteuning voor systeem eigen tableau is nu beschikbaar in azure AD-toepassingsproxy

**Type:** Gewijzigde functie **service categorie:** app-proxy **Product mogelijkheid:** Access Control

Met onze update van de OpenID connect verbinding maken met het OAuth 2,0 code Grant-protocol voor ons pre-verificatie protocol, hoeft u geen aanvullende configuratie meer te doen voor het gebruik van tableau met toepassings proxy. Deze protocol wijziging helpt ook toepassings proxy om meer moderne apps te ondersteunen door alleen HTTP-omleidingen te gebruiken, die algemeen worden ondersteund in Java script en HTML-tags.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nieuwe ondersteuning voor het toevoegen van Google als een id-provider voor B2B-gast gebruikers in Azure Active Directory (preview-versie)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Door Federatie in te stellen met Google in uw organisatie, kunt u aan gebruikers met een eigen Google-account aanmelden bij uw gedeelde apps en resources, zonder dat hiervoor een persoonlijk micro soft-account (Msa's) of een Azure AD-account hoeft te worden gemaakt.

Dit is een open bare preview-versie. Zie [Google als een id-provider voor B2B-gast gebruikers toevoegen](../external-identities/google-federation.md)voor meer informatie over Google Federation.

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Verbeteringen in Azure Active Directory e-mail meldingen

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** Identity Lifecycle Management

E-mail berichten van Azure Active Directory (Azure AD) beschikken nu over een bijgewerkt ontwerp en wijzigingen in het e-mail adres van de afzender en de weergave naam van de afzender, wanneer deze worden verzonden vanuit de volgende services:

- Azure AD-toegangs beoordelingen
- Azure AD Connect Health (Engelstalig)
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Certificaat meldingen voor de Enter prise-app verlopen
- Service meldingen voor de Enter prise-app inrichten

De e-mail meldingen worden verzonden vanaf het volgende e-mail adres en de weergave naam:

- E-mail adres: azure-noreply@microsoft.com
- Weergave naam: Microsoft Azure

Zie [e-mail meldingen in azure AD PIM](../privileged-identity-management/pim-email-notifications.md)voor een voor beeld van een aantal nieuwe e-mail ontwerpen en meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-activiteiten logboeken zijn nu beschikbaar via Azure Monitor

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

De activiteiten logboeken van Azure AD zijn nu beschikbaar in de open bare Preview voor de Azure Monitor (Azure-bewakings service voor het hele platform). Azure Monitor biedt u een lange termijn retentie en naadloze integratie, naast de volgende verbeteringen:

- Lange termijn retentie door uw logboek bestanden te routeren naar uw eigen Azure-opslag account.

- Naadloze SIEM-integratie, zonder dat u aangepaste scripts hoeft te schrijven of te onderhouden.

- Naadloze integratie met uw eigen aangepaste oplossingen, analyse hulpprogramma's of oplossingen voor incident beheer.

Voor meer informatie over deze nieuwe mogelijkheden raadpleegt u onze blog [Azure AD-activiteiten Logboeken in azure monitor Diagnostics is nu beschikbaar in de open bare preview-versie](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) en in onze documentatie [Azure Active Directory activiteiten logboeken in azure monitor (preview)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informatie over voorwaardelijke toegang toegevoegd aan het Azure AD-aanmeld rapport

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** identiteits beveiliging & beveiliging

Met deze update kunt u zien welke beleids regels worden geëvalueerd wanneer een gebruiker zich aanmeldt samen met het resultaat van het beleid. Daarnaast bevat het rapport nu het type client-app dat door de gebruiker wordt gebruikt, zodat u verouderd protocol verkeer kunt identificeren. Rapport vermeldingen kunnen nu ook worden doorzocht op een correlatie-ID, die kan worden gevonden in het fout bericht aan de gebruiker en kan worden gebruikt om de overeenkomende aanmeldings aanvraag te identificeren en op te lossen.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Verouderde verificaties weer geven via activiteiten logboeken voor aanmeldingen

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Met de introductie van het veld **client-app** in de logboeken voor aanmeldings activiteiten kunnen klanten nu zien welke gebruikers oudere verificaties gebruiken. Klanten hebben toegang tot deze informatie met behulp van de aanmeldingen Microsoft Graph API of via de aanmeld activiteiten Logboeken in de Azure AD-Portal, waar u het besturings element **client-app** kunt gebruiken om te filteren op verouderde verificaties. Raadpleeg de documentatie voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juli 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juli 2018 zijn deze 16 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Innovatie hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [bepaalde beheer-SSO](../saas-apps/certainadminsso-tutorial.md), PSUC staging [, IPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screen cast-O-Matic](../saas-apps/screencast-tutorial.md), PowerSchool Unified leslokaal, [Eli onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar externe ondersteuning](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [denkers webvision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow-connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [vaardig heden basis](../saas-apps/skillsbase-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nieuwe gebruikers die SaaS-app-integraties inrichten-juli 2018

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers identiteiten automatiseren in SaaS-toepassingen, zoals Dropbox, Sales Force, ServiceNow en meer. Voor 2018 juli hebben we ondersteuning voor gebruikers inrichting toegevoegd voor de volgende toepassingen in de app-galerie van Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Zie [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md)voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health voor synchronisatie: een eenvoudigere manier om zwevende en dubbele kenmerk synchronisatie fouten op te lossen

**Type:** Nieuwe functie **service categorie:** AD Connect **product Capability:** monitoring & Reporting

Azure AD Connect Health introduceert Self-service herstel om u te helpen bij het markeren en oplossen van synchronisatie fouten. Deze functie lost dubbele kenmerken synchronisatie fouten op en verhelpt objecten die zijn verwijderd uit Azure AD. Deze diagnose biedt de volgende voor delen:

- Hiermee worden gedupliceerde kenmerk synchronisatie fouten beperkt, waardoor specifieke oplossingen worden geboden

- Past een oplossing toe voor specifieke Azure AD-scenario's, waarbij fouten in één stap worden opgelost

- Er is geen upgrade of configuratie vereist om deze functie in te scha kelen en te gebruiken

Zie voor meer informatie [problemen met dubbele kenmerken vaststellen en oplossen](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuele updates voor de Azure AD-en MSA-aanmeld ervaringen

**Type:** Gewijzigde functie **service categorie:** Azure AD- **product functionaliteit:** gebruikers verificatie

De gebruikers interface voor de onlineservices aanmeld procedure van micro soft is bijgewerkt, bijvoorbeeld voor Office 365 en Azure. Met deze wijziging worden de schermen minder overzichtelijk en eenvoudiger. Zie voor meer informatie over deze wijziging de [aanstaande verbeteringen in de blog van Azure AD-aanmeld ervaring](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nieuwe versie van Azure AD Connect-juli 2018

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product Capability:** Identity Lifecycle Management

De nieuwste versie van Azure AD Connect omvat:

- Problemen met oplossingen en ondersteunings updates

- Algemene Beschik baarheid van de Ping-Federate-integratie

- Updates voor de nieuwste SQL 2012-client

Zie [Azure AD Connect: release geschiedenis](../hybrid/reference-connect-version-history.md) van de versie voor meer informatie over deze update.

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Updates voor de gebruiks voorwaarden van de gebruikers interface van de eind gebruiker

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

De acceptatie teken reeks wordt bijgewerkt in de gebruikers interface van de gebruiksrecht overeenkomst.

**Huidige tekst.** U moet de gebruiks voorwaarden accepteren om toegang te krijgen tot resources van [Tenant].<br>**Nieuwe tekst.** Als u toegang wilt krijgen tot de resource [tenantnaam], moet u de gebruiksrecht overeenkomst lezen.

**Huidige tekst:** Als u ervoor kiest om te accepteren, moet u akkoord gaan met alle bovenstaande gebruiks voorwaarden.<br>**Nieuwe tekst:** Klik op accepteren om te bevestigen dat u de gebruiks voorwaarden hebt gelezen en begrepen.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Pass Through-verificatie ondersteunt verouderde protocollen en toepassingen

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Pass-Through-verificatie ondersteunt nu oudere protocollen en apps. De volgende beperkingen worden nu volledig ondersteund:

- Gebruikers aanmeldingen voor oudere Office-client toepassingen, Office 2010 en Office 2013, zonder moderne verificatie.

- Toegang tot agenda delen en beschikbaarheids info in hybride omgevingen van Exchange alleen in Office 2010.

- Aanmelden bij gebruikers van Skype voor bedrijven-client toepassingen zonder moderne verificatie.

- Aanmeldingen van gebruikers naar Power shell-versie 1,0.

- De Apple-Device Enrollment Program (Apple DEP) met behulp van de iOS-Configuratieassistent.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Geconvergeerde beveiligings gegevens beheren voor selfservice voor wachtwoord herstel en Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Met deze nieuwe functie kunnen gebruikers hun beveiligings gegevens beheren (bijvoorbeeld telefoon nummer, e-mail adres, mobiele app enzovoort) voor selfservice voor wachtwoord herstel (SSPR) en Multi-Factor Authentication (MFA) in één ervaring. Gebruikers hoeven niet langer dezelfde beveiligings gegevens voor SSPR en MFA te registreren in twee verschillende ervaringen. Deze nieuwe ervaring is ook van toepassing op gebruikers die een SSPR of MFA hebben.

Als een organisatie geen MFA-of SSPR-registratie afdwingt, kunnen gebruikers hun beveiligings gegevens registreren via de portal **mijn apps** . Van daaruit kunnen gebruikers methoden registreren die zijn ingeschakeld voor MFA of SSPR.

Dit is een open bare preview-versie. Beheerders kunnen de nieuwe ervaring (indien gewenst) inschakelen voor een geselecteerde groep gebruikers of voor alle gebruikers in een Tenant.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>De Microsoft Authenticator-app gebruiken om uw identiteit te verifiëren wanneer u uw wacht woord opnieuw instelt

**Type:** Gewijzigde functie **service categorie:** SSPR- **product mogelijkheid:** gebruikers verificatie

Met deze functie kunnen niet-beheerders hun identiteit verifiëren tijdens het opnieuw instellen van een wacht woord met behulp van een melding of code van Microsoft Authenticator (of een andere verificator-app). Nadat beheerders deze self-service voor het opnieuw instellen van wacht woorden hebben ingeschakeld, kunnen gebruikers die een mobiele app hebben geregistreerd via aka.ms/mfasetup of aka.ms/setupsecurityinfo hun mobiele app gebruiken als een verificatie methode tijdens het opnieuw instellen van hun wacht woord.

De melding voor mobiele apps kan alleen worden ingeschakeld als onderdeel van een beleid dat twee methoden vereist om uw wacht woord opnieuw in te stellen.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Wijzigings bericht: beveiligings oplossing voor de gedelegeerde autorisatie stroom voor apps met behulp van de Azure AD-activiteiten logboeken-API

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** bewaking & rapportage

Vanwege onze sterkere beveiligings handhaving hebben we een wijziging aangebracht in de machtigingen voor apps die gebruikmaken van een gedelegeerde autorisatie stroom om toegang te krijgen tot de [api's van Azure AD-activiteiten logboeken](../reports-monitoring/concept-reporting-api.md). Deze wijziging gaat in op **26 juni 2018**.

Als uw apps Azure AD-activiteiten logboek-Api's gebruiken, voert u de volgende stappen uit om ervoor te zorgen dat de app niet wordt onderbroken nadat de wijziging is opgegaan.

**Uw app-machtigingen bijwerken**

1. Meld u aan bij de Azure Portal, selecteer **Azure Active Directory** en selecteer vervolgens **app-registraties**.
2. Selecteer uw app die gebruikmaakt van de API voor Azure AD-activiteiten logboeken, selecteer **instellingen**, selecteer **vereiste machtigingen** en selecteer vervolgens de **Windows Azure Active Directory** -API.
3. Schakel in het gebied **gedelegeerde machtigingen** van de Blade **toegang inschakelen** het selectie vakje in naast **Directory gegevens lezen** en selecteer vervolgens **Opslaan**.
4. Selecteer **machtigingen verlenen** en selecteer vervolgens **Ja**.

    >[!Note]
    >U moet een globale beheerder zijn om machtigingen voor de app te kunnen verlenen.

Zie het gedeelte [machtigingen verlenen](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) van de vereisten voor toegang tot het Azure AD Reporting API-artikel voor meer informatie.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>TLS-instellingen configureren om verbinding te maken met Azure AD-Services voor PCI DSS compatibiliteit

**Type:** Nieuwe functie **service categorie:** n.v.t. **product capaciteit:** platform

Transport Layer Security (TLS) is een protocol dat privacy-en gegevens integriteit mogelijk maakt tussen twee communicatie toepassingen en het meest gebruikte beveiligings protocol dat momenteel wordt gebruikt.

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft vastgesteld dat vroege versies van TLS en Secure Sockets Layer (SSL) moeten worden uitgeschakeld in het voor deel van het inschakelen van nieuwe en veiligere app-protocollen, met naleving vanaf **30 juni 2018**. Deze wijziging betekent dat als u verbinding maakt met Azure AD-Services en PCI DSS-naleving vereist, u TLS 1,0 moet uitschakelen. Er zijn meerdere versies van TLS beschikbaar, maar TLS 1,2 is de meest recente versie die beschikbaar is voor Azure Active Directory Services. We raden u ten zeerste aan om rechtstreeks naar TLS 1,2 te gaan voor client-en server-en browser-en server combinaties.

Verouderde browsers ondersteunen mogelijk geen nieuwere TLS-versies, zoals TLS 1,2. Als u wilt zien welke versies van TLS door uw browser worden ondersteund, gaat u naar de [QUALYS SSL Labs](https://www.ssllabs.com/) -site en klikt u op **uw browser testen**. U wordt aangeraden om een upgrade uit te voeren naar de nieuwste versie van uw webbrowser en bij voor keur alleen TLS 1,2 in te scha kelen.

**TLS 1,2 inschakelen, per browser**

- **Micro soft Edge en Internet Explorer (beide worden ingesteld met Internet Explorer)**

    1. Open Internet Explorer, selecteer **extra**  >  **geavanceerde Internet opties**  >  .
    2. Selecteer in het gebied **beveiliging** de optie **TLS 1,2 gebruiken** en selecteer vervolgens **OK**.
    3. Sluit alle browser vensters en start Internet Explorer opnieuw.

- **Google Chrome**

    1. Open Google Chrome, typ *Chrome://Settings/* in de adres balk en druk op **Enter**.
    2. Vouw de **Geavanceerde** opties uit, ga naar het gebied **systeem** en selecteer **proxy-instellingen openen**.
    3. Selecteer in het vak **Internet eigenschappen** het tabblad **Geavanceerd** , ga naar het gebied **beveiliging** , selecteer **TLS 1,2 gebruiken** en selecteer vervolgens **OK**.
    4. Sluit alle browser vensters en start Google Chrome opnieuw op.

- **Mozilla Firefox**

    1. Open Firefox, typ *about: config* in de adres balk en druk op **Enter**.
    2. Zoek naar de term *TLS* en selecteer vervolgens de vermelding **Security. TLS. version. Max** .
    3. Stel de waarde in op **3** om te zorgen dat de browser Maxi maal versie TLS 1,2 gebruikt en selecteer **OK**.

        >[!NOTE]
        >Versie 60,0 van Firefox ondersteunt TLS 1,3, dus u kunt ook de waarde Security. TLS. version. Max instellen op **4**.

    4. Sluit alle browser vensters en Start Mozilla Firefox opnieuw.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-juni 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In juni 2018 hebben we deze 15 nieuwe apps met federatieve ondersteuning toegevoegd aan de app-galerie:

[Skytap](../saas-apps/skytap-tutorial.md), het [afwikkelen van muziek](../saas-apps/settlingmusic-tutorial.md), het [SAML 1,1-token ingeschakelde LOB-app](../saas-apps/saml-tutorial.md), [supersfeer](../saas-apps/supermood-tutorial.md), [autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [endpoint backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TONICDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [share point on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [topcx Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [chronischex](../saas-apps/chronicx-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md). Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-wachtwoord beveiliging is beschikbaar als open bare preview

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** gebruikers verificatie

Gebruik Azure AD-wachtwoord beveiliging om eenvoudig te raden wacht woorden uit uw omgeving te verwijderen. Door deze wacht woorden te elimineren, wordt het risico van inbreuk op een type aanval op wacht woord verminderd.

Met name Azure AD-wachtwoord beveiliging helpt u bij het volgende:

- Beveilig de accounts van uw organisatie in zowel Azure AD als Windows Server Active Directory (AD).
- Hiermee wordt voor komen dat gebruikers wacht woorden gebruiken voor een lijst met meer dan 500 van de meest gebruikte wacht woorden en meer dan 1.000.000 teken vervanging van deze wacht woorden.
- Beheer Azure AD-wachtwoord beveiliging vanaf één locatie in de Azure AD-portal voor zowel Azure AD als on-premises Windows Server AD.

Zie voor meer informatie over Azure AD-wachtwoord beveiliging [het elimineren van ongeldige wacht woorden in uw organisatie](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe sjabloon voor beleid voor voorwaardelijke toegang van alle gasten gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe beleids sjabloon voor voorwaardelijke toegang gemaakt voor alle gasten en alle apps. Met deze nieuwe beleids sjabloon past u de nieuwe gebruiks voorwaarden toe, waarmee u het proces voor het maken en afdwingen voor gasten kunt stroom lijnen.

Zie [Azure Active Directory gebruiksvoorwaarden functie](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe aangepaste beleids sjabloon voor voorwaardelijke toegang die is gemaakt tijdens het maken van de gebruiksrecht overeenkomst

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** governance

Tijdens het maken van de gebruiks voorwaarden wordt ook een nieuwe ' aangepaste beleids sjabloon voor voorwaardelijke toegang gemaakt. Met deze nieuwe beleids sjabloon kunt u de gebruiks voorwaarden maken en vervolgens direct naar de Blade voor het maken van beleid voor voorwaardelijke toegang gaan, zonder dat u hand matig door de portal hoeft te navigeren.

Zie [Azure Active Directory gebruiksvoorwaarden functie](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Nieuwe en uitgebreide richt lijnen voor het implementeren van Azure AD Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** identiteits beveiliging & beveiliging

We hebben nieuwe stapsgewijze richt lijnen uitgebracht voor het implementeren van Azure AD Multi-Factor Authentication (MFA) in uw organisatie.

Als u de MFA-implementatie handleiding wilt weer geven, gaat u naar de hand leiding voor [identiteits implementatie](./active-directory-deployment-plans.md) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD gedelegeerde app management-rollen bevinden zich in de open bare preview

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product capability:** Access Control

Beheerders kunnen nu app-beheer taken delegeren zonder de rol van globale beheerder toe te wijzen. De nieuwe functies en mogelijkheden zijn:

- **Nieuwe standaard Azure AD-beheerders rollen:**

    - **Toepassings beheerder.** Biedt de mogelijkheid om alle aspecten van alle apps te beheren, waaronder registratie, SSO-instellingen, app-toewijzingen en licenties, instellingen voor app-proxy en toestemming (met uitzonde ring van Azure AD-resources).

    - **Cloud toepassings beheerder.** Verleent alle toepassings beheerder-vaardig heden, met uitzonde ring van app proxy, omdat deze geen on-premises toegang biedt.

    - **Application Developer.** Biedt de mogelijkheid om app-registraties te maken, zelfs als de optie **gebruikers toestaan apps te registreren** is uitgeschakeld.

- **Eigendom (per app-registratie en per bedrijfs-app instellen, vergelijkbaar met het groeps eigenaars proces:**

    - **Eigenaar van app-registratie.** Biedt de mogelijkheid om alle aspecten van app-registratie te beheren, met inbegrip van het app-manifest en toevoegen van extra eigen aren.

    - **Eigenaar van de Enter prise-app.** Biedt de mogelijkheid om veel aspecten van bedrijfs-apps te beheren, waaronder SSO-instellingen, app-toewijzingen en toestemming (met uitzonde ring van Azure AD-resources).

Voor meer informatie over open bare preview-versie, zie de [open bare preview-functie voor Azure AD-gedelegeerde toepassingen.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blogs. Zie [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md)voor meer informatie over rollen en machtigingen.

---

## <a name="may-2018"></a>Mei 2018

### <a name="expressroute-support-changes"></a>ExpressRoute ondersteunen wijzigingen

**Type:** Planning voor wijziging **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** platform

Software als een service aanbieding, zoals Azure Active Directory (Azure AD), is ontworpen om het beste te kunnen werken door rechtstreeks via internet te gaan, zonder dat hiervoor ExpressRoute of andere particuliere VPN-tunnels zijn vereist. Daarom zullen we op **1 augustus 2018** geen ondersteuning meer bieden voor ExpressRoute voor Azure AD-Services met behulp van open bare Azure-peering en Azure-Community's in micro soft-peering. Services die van invloed zijn op deze wijziging, merken mogelijk Azure AD-verkeer geleidelijk af van ExpressRoute naar Internet.

Hoewel we onze ondersteuning wijzigen, weten we ook dat er nog steeds situaties zijn waarin u mogelijk een speciale set circuits voor uw verificatie verkeer moet gebruiken. Hierdoor blijft Azure AD ondersteuning bieden voor de IP-bereik beperkingen per Tenant met behulp van ExpressRoute en services die al op micro soft-peering zijn aangesloten met de community ' other Office 365 Online Services '. Als uw services worden beïnvloed, maar u ExpressRoute nodig hebt, moet u het volgende doen:

- **Als u gebruikmaakt van open bare Azure-peering.** Ga naar micro soft-peering en meld u aan voor de **andere Office 365 Online Services (12076:5100)-** community. Zie het artikel [een openbaar peering naar micro soft-peering verplaatsen](../../expressroute/how-to-move-peering.md) voor meer informatie over het verplaatsen van open bare Azure-peering naar micro soft-peering.

- **Als u gebruikmaakt van micro soft-peering.** Meld u aan voor de **andere Office 365 online service-Community (12076:5100)** . Zie de [sectie ondersteuning voor BGP-community's](../../expressroute/expressroute-routing.md#bgp) van het artikel ExpressRoute Routing Requirements (Engelstalig) voor meer informatie over routerings vereisten.

Als u speciale circuits moet blijven gebruiken, moet u contact opnemen met uw micro soft-account team over het verkrijgen van toestemming voor het gebruik van de **andere Office 365 online service-Community (12076:5100)** . Op het MS Office-Managed Review Board wordt gecontroleerd of u deze circuits nodig hebt en weet u zeker dat u de technische implicaties begrijpt van het bijhouden van de stroom. Bij niet-geautoriseerde abonnementen voor het maken van route filters voor Office 365 wordt een fout bericht weer gegeven.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph Api's voor beheer scenario's voor gebruiks voorwaarden

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product mogelijkheid:** ontwikkelaars ervaring

We hebben Microsoft Graph-Api's toegevoegd voor de beheer bewerking van Azure AD-gebruiks voorwaarden. U kunt het object voor waarden van het gebruik maken, bijwerken en verwijderen.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Een Azure AD-eind punt voor meerdere tenants toevoegen als een id-provider in Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

Met aangepaste beleids regels kunt u nu het gemeen schappelijke eind punt van Azure AD toevoegen als een id-provider in Azure AD B2C. Op deze manier kunt u één toegangs punt hebben voor alle Azure AD-gebruikers die zich aanmelden bij uw toepassingen. Zie [Azure Active Directory B2C: gebruikers toestaan zich aan te melden bij een multi tenant Azure ad-id-provider met behulp van aangepast beleid](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)voor meer informatie.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Gebruik interne Url's voor toegang tot apps vanaf elke locatie met onze aanmeld extensie voor mijn apps en Azure AD-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

Gebruikers hebben nu toegang tot toepassingen via interne Url's, zelfs wanneer ze zich buiten het bedrijfs netwerk bevinden met behulp van de beveiligde aanmeldings extensie voor mijn apps voor Azure AD. Dit werkt met alle toepassingen die u hebt gepubliceerd met behulp van Azure AD-toepassingsproxy, op elke browser waarop ook de browser uitbreiding van het toegangs venster is geïnstalleerd. De functie voor URL-omleiding wordt automatisch ingeschakeld wanneer een gebruiker zich aanmeldt bij de extensie. De uitbrei ding is beschikbaar voor down loads van [micro soft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)en [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-gegevens in Europa voor Europa-klanten

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** GoLocal

Klanten in Europa moeten hun gegevens in Europa blijven en niet worden gerepliceerd buiten de Europese data centers voor de privacy-en Europese wetgeving van de vergadering. Dit [artikel](./active-directory-data-storage-eu.md) bevat de specifieke details over welke identiteits gegevens worden opgeslagen in Europa en geeft ook informatie over informatie die buiten Europese data centers zal worden opgeslagen.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nieuwe gebruikers die SaaS-app-integraties inrichten-mei 2018

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers identiteiten automatiseren in SaaS-toepassingen, zoals Dropbox, Sales Force, ServiceNow en meer. Voor mei 2018 hebben we ondersteuning voor het inrichten van gebruikers toegevoegd voor de volgende toepassingen in de app-galerie van Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Zie voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure AD-galerie [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-toegangs beoordelingen van groepen en app-toegang biedt nu terugkerende Recensies

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

Toegangs beoordeling van groepen en apps is nu algemeen beschikbaar als onderdeel van Azure AD Premium P2.  Beheerders kunnen toegangs Beoordelingen voor groepslid maatschappen en toepassings toewijzingen configureren om automatisch te laten terugkeren met regel matige tussen pozen, zoals maandelijks of per kwar taal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-activiteiten Logboeken (aanmeldingen en controleren) zijn nu beschikbaar via MS Graph

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Azure AD-activiteiten logboeken, die, inclusief aanmeldingen en audit logboeken, zijn nu beschikbaar via de Microsoft Graph-API. Er zijn twee eind punten beschikbaar via de Microsoft Graph-API voor toegang tot deze logboeken. Bekijk onze [documenten](../reports-monitoring/concept-reporting-api.md) voor programmatische toegang tot Azure AD Reporting api's om aan de slag te gaan.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Verbeteringen in het B2B-inwisselings proces en een organisatie verlaten

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

**Just-in-time-inwisseling:** Nadat u een resource met een gast-API hebt gedeeld, hoeft u geen speciale e-mail met een uitnodiging te verzenden. In de meeste gevallen heeft de gast gebruiker toegang tot de bron en wordt de inwisselings ervaring gewoon in tijd genomen. Er is geen gevolgen meer als gevolg van gemist e-mail berichten. Niet meer vragen om uw gast gebruikers "had u geklikt op die opname koppeling het systeem heeft u gestuurd?". Dit betekent dat wanneer SPO de uitnodigings Manager gebruikt: Cloud bijlagen kunnen dezelfde canonieke URL hebben voor alle gebruikers – intern en extern – in elke staat van inwisseling.

**Moderne aflossings ervaring:** Geen pagina meer gesplitste scherm opname. Gebruikers krijgen een moderne toestemming te zien met de privacyverklaring van de uitgenodigde organisatie, net als bij apps van derden.

**Gast gebruikers kunnen de organisatie verlaten:** Zodra de relatie van een gebruiker met een organisatie is afgelopen, kunnen ze zelf de organisatie verlaten. U hoeft de beheerder van de uitnodigende organisatie niet meer aan te roepen om te worden verwijderd. er worden geen ondersteunings tickets meer verhoogd.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie-mei 2018

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In mei 2018 hebben we deze 18 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [ENVI MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), [montage online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), openspoel, [Arc Publishing-SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [riskware](../saas-apps/riskware-tutorial.md), [koppel](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nieuwe stapsgewijze implementatie handleidingen voor Azure Active Directory

**Type:** Nieuwe functie **service categorie:** andere **product capaciteit:** Directory

Nieuwe stapsgewijze richt lijnen voor het implementeren van Azure Active Directory (Azure AD), waaronder selfservice voor wachtwoord herstel (SSPR), eenmalige aanmelding (SSO), voorwaardelijke toegang (CA), app proxy, User provisioning, Active Directory Federation Services (ADFS) voor Pass-Through-verificatie (PTA) en ADFS to Password Hash Sync (PHS).

Als u de implementatie handleidingen wilt weer geven, gaat u naar de [hand leidingen voor identiteits implementatie](./active-directory-deployment-plans.md) opslag plaats op github. Als u feedback wilt geven over de implementatie handleidingen, gebruikt u het [feedback formulier voor het implementatie plan](https://aka.ms/deploymentplanfeedback). Als u vragen hebt over de implementatie handleidingen, kunt u contact met ons opnemen via [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Bedrijfs toepassingen zoeken-meer apps laden

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Hebt u problemen met het vinden van uw toepassingen/service-principals? We hebben de mogelijkheid toegevoegd om meer toepassingen te laden in de lijst met alle toepassingen van uw bedrijfs toepassingen. Standaard worden er 20 toepassingen weer gegeven. U kunt nu klikken, **meer laden** om extra toepassingen weer te geven.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>De mei-release van AADConnect bevat een open bare preview van de integratie met PingFederate, belang rijke beveiligings updates, veel oplossingen voor fouten en nieuwe fantastische nieuwe hulpprogram ma's voor probleem oplossing.

**Type:** Gewijzigde functie **service categorie:** AD Connect **product Capability:** Identity Lifecycle Management

De mei-release van AADConnect bevat een open bare preview van de integratie met PingFederate, belang rijke beveiligings updates, veel oplossingen voor fouten en nieuwe fantastische nieuwe hulpprogram ma's voor probleem oplossing. U vindt [hier](../hybrid/reference-connect-version-history.md)de release opmerkingen.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-toegangs beoordelingen: automatisch Toep assen

**Type:** Gewijzigde functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** governance

Toegangs beoordelingen van groepen en apps zijn nu algemeen beschikbaar als onderdeel van Azure AD Premium P2. Een beheerder kan configureren om de wijzigingen van de revisor voor die groep of app automatisch toe te passen wanneer de toegangs beoordeling is voltooid. De beheerder kan ook opgeven wat er gebeurt met de voortdurende toegang van de gebruiker als revisoren niet hebben gereageerd, toegang verwijderen, toegang houden of systeem aanbevelingen nemen.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-tokens kunnen niet meer worden geretourneerd met de query response_mode voor nieuwe apps.

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Apps die zijn gemaakt op of na 25 april 2018 kunnen geen **id_token** meer aanvragen met behulp van de **query** response_mode.  Dit brengt Azure AD inline met de OIDC-specificaties en vermindert de kwets baarheid voor uw apps.  Apps die zijn gemaakt vóór 25 april 2018, worden niet geblokkeerd voor het gebruik van de **query** response_mode met een response_type van **id_token**.  De geretourneerde fout bij het aanvragen van een id_token van Azure AD is **AADSTS70007: ' query ' is geen ondersteunde waarde van ' response_mode ' bij het aanvragen van een token**.

Het **fragment** en **form_post** response_modes blijven werken: bij het maken van nieuwe toepassings objecten (bijvoorbeeld voor het gebruik van de app-proxy), moet u ervoor zorgen dat een van deze response_modes wordt gebruikt voordat een nieuwe toepassing wordt gemaakt.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C toegangs token is GA

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U hebt nu toegang tot Web-Api's die worden beveiligd door Azure AD B2C met behulp van toegangs tokens. De functie wordt verplaatst van de open bare preview naar GA. De gebruikers interface-ervaring voor het configureren van Azure AD B2C toepassingen en Web-Api's is verbeterd en er zijn andere kleine verbeteringen aangebracht.

Zie [Azure AD B2C: toegangs tokens aanvragen](../../active-directory-b2c/access-tokens.md)voor meer informatie.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Configuratie van eenmalige aanmelding voor op SAML gebaseerde toepassingen testen

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Bij het configureren van op SAML gebaseerde SSO-toepassingen kunt u de integratie op de pagina configuratie testen. Als er een fout optreedt tijdens het aanmelden, kunt u de fout in de test ervaring opgeven en Azure AD voorziet in oplossings stappen om het specifieke probleem op te lossen.

Zie voor meer informatie:

- [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](../manage-apps/view-applications-portal.md) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
- [Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-gebruiks voorwaarden hebben nu per gebruiker rapporteert

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Beheerders kunnen nu een bepaalde gebruiks voorwaarden selecteren en alle gebruikers weer geven die hebben ingestemd op die gebruiks voorwaarden en de datum/tijd waarop ze hebben plaatsgevonden.

Zie de [functie gebruiks voorwaarden van Azure AD](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riskant IP-adres voor AD FS beveiliging van vergren deling

**Type:** Nieuwe functie **service categorie:** andere **product mogelijkheden:** bewaking & rapportage

Connect Health ondersteunt nu de mogelijkheid om IP-adressen te detecteren die de drempel van mislukte U/P-aanmeldingen per uur of per dag overschrijden. De mogelijkheden van deze functie zijn:

- Uitgebreid rapport met het IP-adres en het aantal mislukte aanmeldingen dat per uur per dag wordt gegenereerd met aanpas bare drempel waarde.
- Op e-mail gebaseerde waarschuwingen die aangeven wanneer een specifiek IP-adres de drempel van mislukte U/P-aanmeldingen per uur/dagelijks heeft overschreden.
- Een download optie om een gedetailleerde analyse van de gegevens uit te voeren

Zie [riskive IP Report](../hybrid/how-to-connect-health-adfs.md)(Engelstalig) voor meer informatie.

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Eenvoudige app-configuratie met bestand of URL van meta gegevens

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

Op de pagina bedrijfs toepassingen kunnen beheerders een SAML-meta gegevensbestand uploaden om op SAML gebaseerde aanmelding te configureren voor de Azure AD-galerie en de niet-galerie toepassing.

Daarnaast kunt u de URL van de Azure AD-toepassing voor federatieve meta gegevens gebruiken voor het configureren van eenmalige aanmelding met de doel toepassing.

Zie [eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassings galerie bevinden](../manage-apps/view-applications-portal.md)voor meer informatie.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Gebruiksvoorwaarden nu algemeen verkrijgbaar

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving


Azure AD-gebruiks voorwaarden zijn verplaatst van de open bare preview naar algemeen beschikbaar.

Zie de [functie gebruiks voorwaarden van Azure AD](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen aan B2B-gebruikers van specifieke organisaties toestaan of blokkeren

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C


U kunt nu opgeven welke partner organisaties u wilt delen en samen werken met in azure AD B2B-samen werking. Als u dit wilt doen, kunt u een lijst maken met specifieke domeinen voor toestaan of weigeren. Wanneer een domein wordt geblokkeerd met behulp van deze mogelijkheden, kunnen werk nemers geen uitnodigingen meer verzenden naar personen in dat domein.

Zo kunt u de toegang tot uw resources beheren, terwijl u een soepele ervaring voor goedgekeurde gebruikers inschakelt.

Deze functie voor B2B-samen werking is beschikbaar voor alle Azure Active Directory klanten en kan worden gebruikt in combi natie met Azure AD Premium functies zoals voorwaardelijke toegang en identiteits beveiliging voor een gedetailleerdere controle van wanneer en hoe externe zakelijke gebruikers zich aanmelden en toegang krijgen.

Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](../external-identities/allow-deny-list.md)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In april 2018 hebben we deze 13 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

Criterium HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [organigram Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta performance monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [stroomx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), schap, [SafetyNET](../saas-apps/safetynet-tutorial.md)

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-gebruikers in azure AD toegang verlenen tot uw on-premises toepassingen (open bare preview)

**Type:** Nieuwe functie **service categorie:** B2B- **product functionaliteit:** B2B/B2C

Als organisatie die gebruikmaakt van Azure Active Directory (Azure AD) B2B-samenwerkings mogelijkheden om gast gebruikers van partner organisaties te uitnodigen voor uw Azure AD, kunt u deze B2B-gebruikers nu toegang bieden tot on-premises apps. Deze on-premises apps kunnen gebruikmaken van op SAML gebaseerde verificatie of geïntegreerde Windows-verificatie (IWA) met Kerberos-beperkte delegering (KCD).

Zie [B2B-gebruikers in azure AD-toegang verlenen aan uw on-premises toepassingen](../external-identities/hybrid-cloud-to-on-premises.md)voor meer informatie.

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Zelf studies voor SSO-integratie ophalen via Azure Marketplace

**Type:** Gewijzigde functie **service categorie:** andere **product capaciteit:** externe integratie

Als een toepassing die in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) wordt vermeld, ondersteuning biedt voor eenmalige aanmelding op basis van SAML, kunt u op **Get it nu** de zelf studie voor integratie koppelen die aan die toepassing is gekoppeld.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snellere prestaties van automatische gebruikers inrichting voor SaaS-toepassingen in azure AD

**Type:** Gewijzigde functie **service categorie:** app Provisioning **product mogelijkheid:** externe integratie

Voorheen kunnen klanten die de Azure Active Directory User Provisioning connectors voor SaaS-toepassingen (bijvoorbeeld Sales Force, ServiceNow en box) gebruiken, trage prestaties ondervinden als hun Azure AD-tenants zich in meer dan 100.000 gecombineerde gebruikers en groepen bevinden, en ze gebruikers-en groeps toewijzingen gebruiken om te bepalen welke gebruikers moeten worden ingericht.

Op 2 april 2018 zijn er belang rijke verbeteringen voor prestaties geïmplementeerd in de Azure AD-inrichtings service die de benodigde tijd voor het uitvoeren van initiële synchronisaties tussen Azure Active Directory en de doel-SaaS-toepassingen aanzienlijk reduceert.

Als gevolg hiervan worden veel klanten die aanvankelijk hebben gesynchroniseerd naar apps die veel dagen of nog nooit hebben voltooid, binnen een paar minuten of uren voltooid.

Zie [Wat gebeurt er tijdens het inrichten?](../..//active-directory/app-provisioning/how-provisioning-works.md) voor meer informatie.

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Selfservice voor wachtwoord herstel van Windows 10-vergrendelings scherm voor hybride Azure AD gekoppelde computers

**Type:** Gewijzigde functie **service categorie:** self-service voor wacht woord opnieuw instellen **:** gebruikers verificatie

We hebben de SSPR-functie van Windows 10 bijgewerkt zodat deze ondersteuning biedt voor machines die zijn toegevoegd aan hybride Azure AD. Deze functie is beschikbaar in Windows 10 RS4. Hiermee kunnen gebruikers hun wacht woord opnieuw instellen vanaf het vergrendelings scherm van een Windows 10-computer. Gebruikers die zijn ingeschakeld en geregistreerd voor selfservice voor wachtwoord herstel, kunnen gebruikmaken van deze functie.

Zie [Azure AD-wacht woord opnieuw instellen in het aanmeldings scherm](../authentication/howto-sspr-windows.md)voor meer informatie.

---

## <a name="march-2018"></a>Maart 2018

### <a name="certificate-expire-notification"></a>Melding dat het certificaat verloopt

**Type:** Vaste **service categorie:** Enter prise apps **product Capability:** SSO

Azure AD verzendt een melding wanneer een certificaat voor een galerie of niet-galerie toepassing bijna is verlopen.

Sommige gebruikers hebben geen meldingen ontvangen voor bedrijfs toepassingen die zijn geconfigureerd voor eenmalige aanmelding op basis van SAML. Dit probleem is opgelost. Azure AD verzendt een melding voor certificaten die verlopen zijn in 7, 30 en 60 dagen. U kunt deze gebeurtenis zien in de audit Logboeken.

Zie voor meer informatie:

- [Certificaten beheren voor federatieve eenmalige aanmelding in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Controleactiviteitenrapporten in Azure Active Directory Portal](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-en GitHub-id-providers in Azure AD B2C

**Type:** Nieuwe functie **service categorie:** B2C-Consumer Identity Management- **product mogelijkheid:** B2B/B2C

U kunt nu Twitter of GitHub toevoegen als een id-provider in Azure AD B2C. Twitter wordt verplaatst van open bare preview naar GA. GitHub wordt uitgebracht in een open bare preview.

Zie [Wat is Azure AD B2B-samen werking?](../external-identities/what-is-b2b.md)voor meer informatie.

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Browser toegang beperken met Intune Managed Browser met voorwaardelijke toegang op basis van een toepassing voor Azure AD voor iOS en Android

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

**Nu beschikbaar in de open bare preview.**

**INTUNE Managed browser SSO:** Uw werk nemers kunnen eenmalige aanmelding gebruiken voor systeem eigen clients (zoals micro soft Outlook) en de Intune Managed Browser voor alle apps die zijn verbonden met Azure AD.

**Ondersteuning voor voorwaardelijke toegang intune Managed browser:** U kunt werk nemers nu de intune Managed browser gebruiken met behulp van beleid voor voorwaardelijke toegang op basis van toepassingen.

Meer informatie hierover vindt u in onze [blog post](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van toepassingen instellen](../conditional-access/app-based-conditional-access.md)

- [Managed browser-beleid configureren](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App-proxy-cmdlets in Power shell GA-module

**Type:** Nieuwe functie **service categorie:** app proxy- **Product mogelijkheid:** Access Control

Ondersteuning voor toepassings proxy-cmdlets is nu beschikbaar in de Power shell-module. Hiervoor moet u de Power shell-modules bijgewerkt blijven: als u meer dan een jaar achter raakt, werken sommige cmdlets niet meer.

Zie [AzureAD](/powershell/module/Azuread/)voor meer informatie.

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 native-clients worden ondersteund door naadloze SSO met behulp van een niet-interactief Protocol

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Gebruiker die gebruikmaakt van Office 365 native clients (versie 16.0.8730. xxxx en hoger), krijgt de mogelijkheid om zich aan te melden met naadloze SSO. Deze ondersteuning wordt geboden door de toevoeging van een niet-interactief Protocol (WS-Trust) aan Azure AD.

Zie [Hoe werkt aanmelden op een systeem eigen client met naadloze SSO?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work) voor meer informatie.

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Gebruikers krijgen een stille aanmelding, met naadloze SSO, als een toepassing aanmeldings aanvragen verzendt naar de Tenant-eind punten van Azure AD

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Gebruikers krijgen een stille aanmelding, met naadloze SSO, als een toepassing (bijvoorbeeld `https://contoso.sharepoint.com` ) aanmeldings aanvragen verzendt naar de Tenant eindpunten van Azure AD, dat wil zeggen, `https://login.microsoftonline.com/contoso.com/<..>` of `https://login.microsoftonline.com/<tenant_ID>/<..>` -in plaats van het gemeen schappelijke eind punt () van Azure AD `https://login.microsoftonline.com/common/<...>` .

Zie [Azure Active Directory naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md)voor meer informatie.

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>U hoeft slechts één Azure AD-URL, in plaats van twee Url's, toe te voegen aan de intranet zone-instellingen van gebruikers om naadloze SSO uit te vouwen

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Als u naadloze SSO wilt implementeren voor uw gebruikers, moet u slechts één Azure AD-URL toevoegen aan de intranet zone-instellingen van de gebruikers met behulp van groeps beleid in Active Directory: `https://autologon.microsoftazuread-sso.com` . Voorheen moesten we twee Url's toevoegen.

Zie [Azure Active Directory naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md)voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In 2018 maart hebben we deze 15 nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), assistent van FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enter prise server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM voor Azure-resources is algemeen beschikbaar

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Als u Azure AD Privileged Identity Management gebruikt voor Directory-rollen, kunt u nu gebruikmaken van tijd afhankelijke toegang en toewijzings mogelijkheden van PIM voor Azure-resource rollen, zoals abonnementen, resource groepen, Virtual Machines en andere bronnen die door Azure Resource Manager worden ondersteund. Multi-Factor Authentication afdwingen bij het activeren van functies just-in-time en het plannen van activeringen in combi natie met goedgekeurde wijzigings Vensters. Daarnaast voegt deze release verbeteringen toe die niet beschikbaar zijn tijdens de open bare preview, inclusief een bijgewerkte gebruikers interface, werk stromen voor goed keuring en de mogelijkheid om rollen uit te breiden die binnenkort verlopen en verlopen rollen vernieuwen.

Zie [PIM voor Azure-resources (preview)](../privileged-identity-management/azure-pim-resource-rbac.md) voor meer informatie

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Optionele claims toevoegen aan uw app-tokens (open bare preview)

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Uw Azure AD-app kan nu aangepaste of optionele claims aanvragen in JWTs-of SAML-tokens.  Dit zijn claims over de gebruiker of Tenant die niet standaard zijn opgenomen in het token, vanwege grootte-of toepas baarheids beperkingen.  Dit is momenteel beschikbaar als open bare Preview voor Azure AD-apps op de eind punten v 1.0 en v 2.0.  Raadpleeg de documentatie voor informatie over welke claims kunnen worden toegevoegd en hoe u het toepassings manifest kunt bewerken om ze aan te vragen.

Zie [optionele claims in azure AD](../develop/active-directory-optional-claims.md)voor meer informatie.

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD ondersteunt PKCE voor meer beveiligde OAuth-stromen

**Type:** Nieuwe functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Azure AD-documenten zijn bijgewerkt met ondersteuning voor PKCE, waarmee veiliger communicatie mogelijk is tijdens de OAuth 2,0-autorisatie code subsidie stroom.  Zowel S256 als code_challenges met lees bare tekst worden ondersteund op de eind punten v 1.0 en v 2.0.

Zie [een autorisatie code aanvragen](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)voor meer informatie.

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Ondersteuning voor het inrichten van alle gebruikers kenmerk waarden die beschikbaar zijn in de workday Get_Workers-API

**Type:** Nieuwe functie **service categorie:** ondersteuning voor het inrichten van apps **:** externe integratie

De open bare preview van de inkomende inrichting van de werkdag naar Active Directory en Azure AD ondersteunt nu de mogelijkheid om alle kenmerk waarden die beschikbaar zijn in de werk dagen Get_Workers API te extra heren en in te richten. Dit voegt ondersteuning toe voor honderden extra standaard-en aangepaste kenmerken dan die die worden geleverd met de eerste versie van de inkomende inrichtings connector van de werkdag.

Zie [de lijst met gebruikers kenmerken van workday aanpassen](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes) voor meer informatie.

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Groepslid maatschap wijzigen van dynamisch in statisch en omgekeerd

**Type:** Nieuwe functie **service categorie:** groeps beheer **product mogelijkheden:** samen werking

Het is mogelijk om te wijzigen hoe het lidmaatschap wordt beheerd in een groep. Dit is handig als u dezelfde groeps naam en ID in het systeem wilt behouden, zodat eventuele bestaande verwijzingen naar de groep nog geldig zijn. Als u een nieuwe groep maakt, moeten deze verwijzingen worden bijgewerkt.
Het Azure AD-beheer centrum is bijgewerkt voor ondersteuning van deze functionaliteit. Klanten kunnen nu bestaande groepen van een dynamisch lidmaatschap omzetten in een toegewezen lidmaatschap en vice versa. De bestaande Power shell-cmdlets zijn ook nog steeds beschikbaar.

Zie voor meer informatie [dynamische lidmaatschaps regels voor groepen in azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Verbeterd gedrag bij afmelden met naadloze SSO

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Voorheen, zelfs als gebruikers zich expliciet afmelden bij een toepassing die is beveiligd door Azure AD, worden ze automatisch opnieuw aangemeld met naadloze SSO als ze opnieuw proberen een Azure AD-toepassing te openen binnen hun Corpnet vanaf hun apparaten die lid zijn van hun domein. Bij deze wijziging wordt afmelden ondersteund.  Hiermee kunnen gebruikers hetzelfde of een ander Azure AD-account kiezen om zich opnieuw aan te melden met, in plaats van dat ze automatisch worden aangemeld met naadloze SSO.

Zie [Azure Active Directory naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md) voor meer informatie

---

### <a name="application-proxy-connector-version-154020-released"></a>Versie van connector voor toepassings proxy 1.5.402.0 uitgebracht

**Type:** Gewijzigde functie **service categorie:** app-proxy **product mogelijkheid:** identiteits beveiliging & beveiliging

Deze connector versie wordt geleidelijk geïmplementeerd tot en met november. Deze nieuwe connector versie bevat de volgende wijzigingen:

- De connector stelt nu cookies op domein niveau in in plaats van subdomeinniveau. Dit zorgt voor een soepelere SSO-ervaring en vermijdt dubbele verificatie prompts.
- Ondersteuning voor gesegmenteerde coderings aanvragen
- Verbeterde status controle van connectors
- Verschillende oplossingen voor oplossingen en stabiliteits verbeteringen

Zie [Wat is Azure AD-toepassingsproxy-connectors](../manage-apps/application-proxy-connectors.md)? voor meer informatie.

---

## <a name="february-2018"></a>Februari 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Verbeterde navigatie voor het beheren van gebruikers en groepen

**Type:** Planning voor **service categorie wijzigen:** Directory Management **product Capability:** Directory

De navigatie-ervaring voor het beheren van gebruikers en groepen is gestroomlijnd. U kunt nu rechtstreeks navigeren vanuit het Directory-overzicht, direct naar de lijst met alle gebruikers, met eenvoudige toegang tot de lijst met verwijderde gebruikers. U kunt ook vanuit het Directory-overzicht rechtstreeks naar de lijst met alle groepen navigeren, met een eenvoudigere toegang tot de instellingen voor groeps beheer. Daarnaast kunt u op de pagina overzicht van mappen zoeken naar een gebruiker, groep, bedrijfs toepassing of app-registratie.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Beschik baarheid van aanmeldingen en controle rapporten in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet)

**Type:** Nieuwe functie **service categorie:** Azure stack **product capaciteit:** bewaking & rapportage

Azure AD-activiteiten logboek rapporten zijn nu beschikbaar in Microsoft Azure beheerd door 21Vianet-exemplaren (Azure China 21Vianet). De volgende logboeken zijn opgenomen:

- **Activiteiten logboeken voor aanmeldingen**  : bevat alle aanmeld logboeken die zijn gekoppeld aan uw Tenant.

- **Audit logboeken voor selfservice wacht woorden** : bevat alle SSPR-controle Logboeken.

- **Controle logboeken voor Directory beheer** : bevat alle controle logboeken met betrekking tot Directory beheer, zoals gebruikers beheer, app-beheer en anderen.

Met deze logboeken kunt u inzicht krijgen in de manier waarop uw omgeving bezig is. Met de gegevens kunt u het volgende doen:

- Bepaal hoe uw apps en services door uw gebruikers worden gebruikt.

- Los problemen op om te voor komen dat uw gebruikers hun werk doen.

Zie [Azure Active Directory Reporting](../reports-monitoring/overview-reports.md)(Engelstalig) voor meer informatie over het gebruik van deze rapporten.

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>De rol Report Reader (niet-beheerdersrol) gebruiken om Azure AD-activiteiten rapporten weer te geven

**Type:** Nieuwe functie **service categorie:** rapportage van **product mogelijkheden:** bewaking & rapportage

Als onderdeel van klanten om niet-beheerders rollen in te scha kelen om toegang te krijgen tot Azure AD-activiteiten logboeken, hebben we de mogelijkheid ingeschakeld voor gebruikers die de rol Report Reader hebben om toegang te krijgen tot aanmeldingen en controle activiteiten binnen de Azure Portal, evenals het gebruik van de Microsoft Graph-API.

Zie [Azure Active Directory Reporting](../reports-monitoring/overview-reports.md)(Engelstalig) voor meer informatie over het gebruik van deze rapporten.

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Aanvraag voor werk nemers beschikbaar als gebruikers kenmerk en gebruikers-id

**Type:** Nieuwe functie **service categorie:** Enter prise apps **product Capability:** SSO

U kunt werk **nemers** configureren als de gebruikers-id en het gebruikers kenmerk voor leden gebruikers en B2B-gasten in op SAML gebaseerde aanmeldings toepassingen vanuit de gebruikers interface van de bedrijfs toepassing.

Zie voor meer informatie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Vereenvoudigd toepassings beheer met behulp van joker tekens in azure AD-toepassingsproxy

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** gebruikers verificatie

We bieden nu ondersteuning voor het publiceren van toepassingen met behulp van joker tekens om de toepassings implementatie eenvoudiger te maken en uw administratieve overhead te verlagen. Als u een Joker toepassing wilt publiceren, kunt u de standaard publicatie stroom van de toepassing volgen, maar een Joker teken gebruiken in de interne en externe Url's.

Zie voor meer informatie [joker tekens toepassingen in de Azure Active Directory toepassings proxy](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nieuwe cmdlets voor de ondersteuning van de configuratie van toepassings proxy

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** platform

De nieuwste versie van de AzureAD Power shell preview-module bevat nieuwe cmdlets waarmee klanten toepassings proxy toepassingen kunnen configureren met behulp van Power shell.

De nieuwe cmdlets zijn:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nieuwe cmdlets om de configuratie van groepen te ondersteunen

**Type:** Nieuwe functie **service categorie:** app proxy **product mogelijkheid:** platform

De meest recente versie van de AzureAD Power shell-module bevat cmdlets voor het beheren van groepen in azure AD. Deze cmdlets zijn eerder beschikbaar in de AzureADPreview-module en zijn nu toegevoegd aan de AzureAD-module

De groeps-cmdlets die nu worden uitgebracht voor algemene Beschik baarheid zijn:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Er is een nieuwe versie van Azure AD Connect beschikbaar

**Type:** Nieuwe functie **service categorie:** AD Sync **product capaciteit:** platform

Azure AD Connect is het voorkeurs programma voor het synchroniseren van gegevens tussen Azure AD en on-premises gegevens bronnen, waaronder Windows Server Active Directory en LDAP.

>[!Important]
>Deze build introduceert wijzigingen in schema's en synchronisatie regels. De Azure AD Connect-synchronisatie service activeert een volledige import-en volledige synchronisatie stappen na een upgrade. Zie voor meer informatie over hoe u dit gedrag kunt wijzigen de [volledige synchronisatie uitstellen na de upgrade](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Deze release bevat de volgende updates en wijzigingen:

**Opgeloste problemen**

- Corrigeer het tijd venster op de achtergrond taken voor de pagina partitie filtering wanneer u overschakelt naar de volgende pagina.

- Er is een fout opgelost die de toegangs fout veroorzaakte tijdens de aangepaste ConfigDB-actie.

- Er is een fout opgelost bij het herstellen van de time-out van de SQL-verbinding.

- Er is een fout opgelost waarbij certificaten met SAN-joker tekens niet voldoen aan de vereisten controle.

- Er is een fout opgelost die ervoor zorgt dat miiserver.exe vastloopt tijdens het exporteren van de Azure AD-connector.

- Er is een fout opgelost waarbij een onjuist wacht woord wordt geregistreerd op de domein controller tijdens het uitvoeren van de Azure AD Connect-wizard om de configuratie te wijzigen

**Nieuwe functies en verbeteringen**

- Applicatie-telemetrie: beheerders kunnen deze klasse van gegevens in-of uitschakelen.

- Status gegevens van Azure AD-beheerders moeten de status Portal bezoeken om hun status instellingen te beheren. Zodra het service beleid is gewijzigd, worden deze door de agents gelezen en afgedwongen.

- De configuratie acties voor het terugschrijven van apparaten en een voortgangs balk voor het initialiseren van pagina's zijn toegevoegd.

- Verbeterde algemene diagnoses met HTML-rapport en volledige gegevens verzameling in een ZIP-Text/HTML-rapport.

- Verbeterde betrouw baarheid van automatische upgrade en extra telemetrie toegevoegd om te zorgen dat de status van de server kan worden bepaald.

- Beperk machtigingen die beschikbaar zijn voor bevoegde accounts op het AD Connector-account. Voor nieuwe installaties beperkt de wizard de machtigingen die privileged accounts hebben op het MSOL-account nadat het MSOL-account is gemaakt. De wijzigingen zijn van invloed op snelle installaties en aangepaste installaties met het automatisch maken van een account.

- Het installatie programma is gewijzigd zodat er geen SA-bevoegdheid vereist is bij een schone installatie van AADConnect.

- Nieuw hulp programma voor het oplossen van synchronisatie problemen voor een specifiek object. Op dit moment controleert het hulp programma op de volgende zaken:

    - De UserPrincipalName van het gesynchroniseerde gebruikers object en het gebruikers account in de Azure AD-Tenant komen niet overeen.

    - Als het object wordt gefilterd op basis van synchronisatie vanwege domein filtering

    - Als het object wordt gefilterd op basis van synchronisatie vanwege het filteren van organisatie-eenheid (OE)

- Nieuw hulp programma voor het synchroniseren van de huidige wacht woord-hash die is opgeslagen in het on-premises Active Directory voor een specifiek gebruikers account. Voor het hulp programma is geen wachtwoord wijziging vereist.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Toepassingen die Intune-app-beveiliging beleid ondersteunen dat is toegevoegd voor gebruik met voorwaardelijke toegang op basis van Azure AD-toepassing

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging & beveiliging

Er zijn meer toepassingen toegevoegd die ondersteuning bieden voor voorwaardelijke toegang op basis van toepassingen. Nu kunt u toegang krijgen tot Office 365 en andere met Azure AD verbonden Cloud-apps met behulp van deze goedgekeurde client-apps.

De volgende toepassingen worden toegevoegd aan het einde van februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Zie voor meer informatie:

- [Vereiste voor client-app vereist](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Gebruiksvoorwaarden update voor de mobiele ervaring

**Type:** Gewijzigde functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Wanneer de gebruiks voorwaarden worden weer gegeven, kunt u nu klikken op **problemen met weer gave? Klik hier**. Als u op deze koppeling klikt, wordt de gebruiksrecht overeenkomst voor uw apparaat geopend. Ongeacht de teken grootte in het document of de scherm grootte van het apparaat, kunt u inzoomen en het document naar behoefte lezen.

---

## <a name="january-2018"></a>Januari 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In januari 2018 zijn de volgende nieuwe apps met ondersteuning voor Federatie toegevoegd in de app-galerie:

[IBM-Openpaginas](../saas-apps/ibmopenpages-tutorial.md), [OneTrust privacy management-software](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk Federated Directory en [betrouw baarheid netvoor delen](../saas-apps/fidelitynetbenefits-tutorial.md).

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="sign-in-with-additional-risk-detected"></a>Meld u aan met een extra risico gedetecteerd

**Type:** Nieuwe functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging & beveiliging

Het inzicht dat u krijgt bij een gedetecteerde risico detectie is gekoppeld aan uw Azure AD-abonnement. Met de Azure AD Premium P2-editie krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

Met de Azure AD Premium P1 Edition worden detecties die niet onder uw licentie vallen, weer gegeven als het aanmelden van de risico detectie met een extra risico gedetecteerd.

Zie [Azure Active Directory-risico detectie](../identity-protection/overview-identity-protection.md)voor meer informatie.

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-toepassingen verbergen via de toegangs Vensters van de eind gebruiker

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

U kunt nu beter beheren hoe Office 365-toepassingen worden weer gegeven op de toegangs Vensters van uw gebruikers via een nieuwe gebruikers instelling. Deze optie is handig voor het verminderen van het aantal apps op de toegangs Vensters van een gebruiker als u alleen Office-apps in de Office-Portal wilt weer geven. De instelling bevindt zich in de **gebruikers instellingen** en is voorzien van een label, **gebruikers kunnen alleen Office 365-apps zien in de Office 365-Portal**.

Zie [een toepassing verbergen van gebruikers ervaring in azure Active Directory](../manage-apps/hide-application-from-user-portal.md)voor meer informatie.

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Naadloos aanmelden bij apps die rechtstreeks zijn ingeschakeld voor wacht woord-SSO vanuit de URL van de app

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** SSO

De browser extensie van mijn apps is nu beschikbaar via een handig hulp programma waarmee u de mogelijkheid voor eenmalige aanmelding van mijn apps kunt gebruiken als snelkoppeling in uw browser. Na de installatie ziet de gebruiker in hun browser een wafel-pictogram dat snelle toegang biedt tot apps. Gebruikers kunnen nu profiteren van:

- De mogelijkheid om zich rechtstreeks aan te melden bij op wacht woord gebaseerde apps vanaf de aanmeldings pagina van de app
- Een app starten met de functie voor snel zoeken
- Snelkoppelingen naar recent gebruikte apps uit de uitbrei ding
- De uitbrei ding is beschikbaar voor micro soft Edge, Chrome en Firefox.

Zie voor meer informatie [mijn apps beveiligde aanmeldings extensie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>De Azure AD-beheer ervaring in Klassieke Azure-portal is buiten gebruik gesteld

**Type:** Afgeschafte **service categorie:** Azure AD- **product mogelijkheid:** map

Vanaf 8 januari 2018 is de Azure AD-beheer ervaring in de klassieke Azure-Portal buiten gebruik gesteld. Dit vond plaats in combi natie met de buiten gebruiks telling van de klassieke Azure-portal zelf. In de toekomst moet u het [Azure AD-beheer centrum](https://aad.portal.azure.com) gebruiken voor al uw op portals gebaseerd beheer van Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>De Phone factor-webportal is buiten gebruik gesteld

**Type:** Afgeschafte **service categorie:** Azure AD- **product mogelijkheid:** map

Vanaf 8 januari 2018 is de Phone factor-webportal buiten gebruik gesteld. Deze portal is gebruikt voor het beheer van MFA-server, maar deze functies zijn verplaatst naar de Azure Portal op portal.azure.com.

De MFA-configuratie bevindt zich op: **Azure Active Directory \> MFA-server**

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afschaffen

**Type:** Afgeschafte **service categorie:** rapportage van **product mogelijkheden:** Identity Lifecycle Management


Met de algemene Beschik baarheid van de nieuwe Azure Active Directory-beheer console en nieuwe Api's die nu beschikbaar zijn voor activiteiten-en beveiligings rapporten, zijn de rapport-Api's onder "/Reports" eind punt buiten 31 december 2017.

**Wat is er beschikbaar?**

Als onderdeel van de overgang naar de nieuwe beheer console hebben we twee nieuwe Api's beschikbaar gesteld voor het ophalen van Azure AD-activiteiten Logboeken. De nieuwe set Api's biedt uitgebreide filter-en sorteer functies, naast het bieden van uitgebreide controle-en aanmeldings activiteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen nu worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.

Zie voor meer informatie:

- [Aan de slag met de API voor Azure Active Directory rapportage](../reports-monitoring/concept-reporting-api.md)

- [Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Gebruiksvoorwaarden in het toegangs venster

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

U kunt nu naar het toegangs venster gaan en de gebruiks voorwaarden bekijken die u eerder hebt geaccepteerd.

Volg deze stappen:

1. Ga naar de [MyApps-Portal](https://myapps.microsoft.com)en meld u aan.

2. Selecteer uw naam in de rechter bovenhoek en selecteer vervolgens **profiel** in de lijst.

3. Selecteer in uw **profiel** **gebruiks voorwaarden bekijken**.

4. Nu kunt u de gebruiks voorwaarden bekijken die u hebt geaccepteerd.

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="new-azure-ad-sign-in-experience"></a>Nieuwe Azure AD-aanmeldings ervaring

**Type:** Nieuwe functie **service categorie:** Azure AD- **product mogelijkheid:** gebruikers verificatie

De UIs van Azure AD en het Microsoft-account-identiteits systeem zijn zodanig ontworpen dat ze een consistent uiterlijk hebben. Daarnaast verzamelt de aanmeldings pagina van Azure AD de gebruikers naam eerst, gevolgd door de referentie op een tweede scherm.

Zie voor meer informatie [de nieuwe Azure AD-aanmeldings ervaring is nu beschikbaar als open bare preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Minder aanmeldings prompts: een nieuwe ervaring ' aangemeld blijven ' voor aanmelding bij Azure AD

**Type:** Nieuwe functie **service categorie:** Azure AD- **product mogelijkheid:** gebruikers verificatie

Het selectie vakje **aangemeld blijven** op de aanmeldings pagina van Azure AD is vervangen door een nieuwe prompt die wordt weer gegeven nadat u zich hebt geverifieerd.

Als u **Ja** op deze vraag reageert, geeft de service u een permanent vernieuwings token. Dit gedrag is hetzelfde als wanneer u het selectie vakje **aangemeld blijven** in de oude ervaring hebt ingeschakeld. Voor federatieve tenants wordt deze prompt weer gegeven nadat u zich met de federatieve service hebt geverifieerd.

Zie voor meer informatie [minder aanmeldings prompts: de nieuwe ervaring ' aangemeld blijven ' voor Azure AD is in Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Configuratie toevoegen om te vereisen dat de gebruiks voorwaarden worden uitgevouwen voordat ze worden geaccepteerd

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Gebruikers moeten de gebruiks voorwaarden uitvouwen voordat ze de voor waarden accepteren.

Selecteer aan **of** **uit** om gebruikers te verplichten de gebruiks voorwaarden uit te breiden. Met de instelling **on** moeten gebruikers de gebruiks voorwaarden weer geven voordat ze deze kunnen accepteren.

Zie de [functie Azure AD-gebruiks voorwaarden (preview)](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Scoped activering voor in aanmerking komende roltoewijzingen

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

U kunt scoped activering gebruiken om in aanmerking komende Azure-resource roltoewijzingen met minder autonomie te activeren dan de oorspronkelijke standaard waarden voor de toewijzing. Een voor beeld is als u wordt toegewezen als de eigenaar van een abonnement in uw Tenant. Als u het bereik hebt geactiveerd, kunt u de rol eigenaar activeren voor Maxi maal vijf resources die zich in het abonnement bevinden (zoals resource groepen en virtuele machines). Het bereik van uw activering vermindert mogelijk de mogelijkheid om ongewenste wijzigingen in essentiële Azure-resources uit te voeren.

Zie [Wat is Azure AD privileged Identity Management?](../privileged-identity-management/pim-configure.md)voor meer informatie.

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nieuwe federatieve apps in de Azure AD-App-galerie

**Type:** Nieuwe functie **service categorie:** Enter prise apps- **product mogelijkheden:** integratie van derden

In december 2017 hebben we deze nieuwe apps met federatieve ondersteuning toegevoegd aan onze app-galerie:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital Store](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CYBSAFE, [FactSet](../saas-apps/factset-tutorial.md), [Image Works](../saas-apps/imageworks-tutorial.md), [mobi](../saas-apps/mobi-tutorial.md), [Mobile Iron Azure AD Integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO voor bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO voor bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD Integration.

Zie voor meer informatie over de apps [SaaS-toepassings integratie met Azure Active Directory](../saas-apps/tutorial-list.md).

Zie [uw toepassing weer geven in de galerie van Azure Active Directory toepassingen](../develop/v2-howto-app-gallery-listing.md)voor meer informatie over het weer geven van uw toepassing in de app-galerie van Azure AD.

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Goedkeurings werk stromen voor Azure AD-adreslijst rollen

**Type:** Gewijzigde functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

De goedkeurings werk stroom voor Azure AD Directory-functies is algemeen beschikbaar.

Met een goedkeurings werk stroom kunnen beheerders met verhoogde bevoegdheid rollen die in aanmerking komen, vereisen om de activering van rollen aan te vragen voordat ze de geprivilegieerde rol kunnen gebruiken. Meerdere gebruikers en groepen kunnen gedelegeerde goedkeurings verantwoordelijkheden zijn. In aanmerking komende leden ontvangen meldingen wanneer goed keuring is voltooid en de bijbehorende rol actief is.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Pass-Through-verificatie: ondersteuning voor Skype voor bedrijven

**Type:** Gewijzigde functie **service categorie:** verificaties (aanmeldingen) **product mogelijkheid:** gebruikers verificatie

Pass-Through-verificatie ondersteunt nu gebruikers aanmeldingen bij Skype voor bedrijven-client toepassingen die ondersteuning bieden voor moderne verificatie, waaronder online-en hybride topologieën.

Zie voor meer informatie [Skype voor bedrijven-topologieën die worden ondersteund door moderne verificatie](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Updates voor Azure AD Privileged Identity Management voor Azure RBAC (preview-versie)

**Type:** Gewijzigde functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Met de open bare preview-vernieuwing van Azure AD Privileged Identity Management (PIM) voor op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u nu het volgende doen:

* Gebruik gewoon voldoende beheer.
* Goed keuring vereisen om resource rollen te activeren.
* Plan een toekomstige activering van een rol die goed keuring vereist voor zowel Azure AD-als Azure-rollen.

Zie [privileged Identity Management voor Azure-resources (preview)](../privileged-identity-management/azure-pim-resource-rbac.md)voor meer informatie.

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Buiten gebruik stellen Access Control

**Type:** Planning voor wijzigings **service categorie:** Access Control-service **Product mogelijkheid:** Access Control service

Azure Active Directory Access Control (ook wel bekend als de Access Control-service), wordt na een eind 2018 afgetrokken. Meer informatie over een gedetailleerd schema en migratie richtlijnen op hoog niveau wordt in de komende weken weer gegeven. U kunt opmerkingen op deze pagina achterlaten met vragen over de Access Control-service en een teamlid beantwoordt deze.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Browser toegang tot de Intune Managed Browser beperken

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt browser toegang tot Office 365 en andere met Azure AD verbonden Cloud-apps beperken door de Intune Managed Browser als goedgekeurde app te gebruiken.

U kunt nu de volgende voor waarde configureren voor voorwaardelijke toegang op basis van een toepassing:

**Client-apps:** Browser

**Wat is het effect van de wijziging?**

De toegang is momenteel geblokkeerd wanneer u deze voor waarde gebruikt. Wanneer de preview beschikbaar is, is het gebruik van de beheerde browser toepassing vereist voor alle toegang.

Zoek deze mogelijkheid en meer informatie in aanstaande blogs en release opmerkingen.

Zie [voorwaardelijke toegang in azure AD](../conditional-access/overview.md)voor meer informatie.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Planning voor wijziging **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn te vinden op de lijst met [goedgekeurde client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Zie voor meer informatie:

- [Vereiste voor client-app vereist](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Ondersteuning voor voor waarden voor meerdere talen

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

Beheerders kunnen nu nieuwe gebruiks voorwaarden maken die meerdere PDF-documenten bevatten. U kunt deze PDF-documenten labelen met een bijbehorende taal. Gebruikers worden de PDF weer gegeven met de overeenkomende taal op basis van hun voor keuren. Als er geen overeenkomst is, wordt de standaard taal weer gegeven.

---

### <a name="real-time-password-writeback-client-status"></a>Status van real-time-client voor terugschrijven van wacht woorden

**Type:** Nieuwe functie **service categorie:** de mogelijkheid van selfservice voor het opnieuw instellen van wacht woorden **:** gebruikers verificatie

U kunt nu de status controleren van uw on-premises client voor het terugschrijven van wacht woorden. Deze optie is beschikbaar in de sectie **on-premises integratie** van de pagina [wacht woord opnieuw instellen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) .

Als er problemen zijn met uw verbinding met uw on-premises terugschrijf client, wordt een fout bericht weer gegeven waarin u het volgende kunt doen:

- Informatie over waarom u geen verbinding kunt maken met uw on-premises terugschrijf client.
- Een koppeling naar documentatie die u helpt bij het oplossen van het probleem.

Zie [on-premises integratie](../authentication/concept-sspr-howitworks.md#on-premises-integration)voor meer informatie.

---

### <a name="azure-ad-app-based-conditional-access"></a>Voorwaardelijke toegang op basis van Azure AD-app

**Type:** Nieuwe functie **service categorie:** Azure AD- **product functionaliteit:** identiteits beveiliging en-beveiliging

U kunt nu de toegang tot Office 365 en andere met Azure AD verbonden Cloud-apps beperken tot [goedgekeurde client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) die ondersteuning bieden voor het intune-beveiligings beleid voor apps met behulp van [voorwaardelijke toegang op basis van Azure AD](../conditional-access/app-based-conditional-access.md). Het beveiligings beleid voor apps in intune wordt gebruikt voor het configureren en beveiligen van Bedrijfs gegevens op deze client toepassingen.

Door [app](../conditional-access/app-based-conditional-access.md) te combi neren met op [apparaten gebaseerd](../conditional-access/require-managed-devices.md) beleid voor voorwaardelijke toegang, hebt u de flexibiliteit om gegevens te beveiligen voor persoonlijke en Bedrijfs apparaten.

De volgende voor waarden en besturings elementen zijn nu beschikbaar voor gebruik met voorwaardelijke toegang op basis van apps:

**Ondersteunde platform voorwaarde**

- iOS
- Android

**Voor waarde voor client-apps**

- Mobiele apps en desktop-clients

**Toegangsbeheer**

- Goedgekeurde client-apps vereisen

Zie [voorwaardelijke toegang op basis van apps voor Azure AD](../conditional-access/app-based-conditional-access.md)voor meer informatie.

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-apparaten beheren in de Azure Portal

**Type:** Nieuwe functie **service categorie:** apparaat registratie en beheer **product mogelijkheid:** identiteits beveiliging en-beveiliging

U kunt nu al uw apparaten vinden die zijn verbonden met Azure AD en de apparaat-gerelateerde activiteiten op één plek. Er is een nieuwe beheer ervaring voor het beheren van al uw apparaat-id's en instellingen in de Azure Portal. In deze release kunt u het volgende doen:

- Bekijk alle apparaten die beschikbaar zijn voor voorwaardelijke toegang in azure AD.
- Eigenschappen weer geven, waaronder uw hybride apparaten die deel uitmaken van Azure AD.
- Zoek BitLocker-sleutels voor uw apparaten die deel uitmaken van Azure AD, beheer uw apparaat met intune en meer.
- Instellingen voor Azure AD-apparaten beheren.

Zie [apparaten beheren met behulp van de Azure Portal](../devices/device-management-azure-portal.md)voor meer informatie.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Ondersteuning voor macOS als een platform voor voorwaardelijke toegang van Azure AD

**Type:** Nieuwe functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt macOS in uw beleid voor voorwaardelijke toegang van Azure AD nu opnemen (of uitsluiten). Met het toevoegen van macOS aan de ondersteunde platformen kunt u het volgende doen:

- **Registreer en beheer macOS-apparaten met behulp van intune.** Net als bij andere platformen, zoals iOS en Android, is een bedrijfs portal-toepassing beschikbaar voor macOS om Unified-inschrijvingen uit te voeren. U kunt de nieuwe bedrijfs portal-app voor macOS gebruiken om een apparaat in te schrijven bij intune en dit te registreren bij Azure AD.
- **Zorg ervoor dat macOS-apparaten voldoen aan het nalevings beleid van uw organisatie dat is gedefinieerd in intune.** U kunt in intune op het Azure Portal nu nalevings beleid instellen voor macOS-apparaten.
- **Beperk de toegang tot toepassingen in azure AD tot alleen compatibele macOS-apparaten.** Het ontwerp van het beleid voor voorwaardelijke toegang heeft macOS als een afzonderlijke platform optie. Nu kunt u macOS-specifiek beleid voor voorwaardelijke toegang ontwerpen voor de doel toepassing die in Azure is ingesteld.

Zie voor meer informatie:

- [Een apparaatnalevingsbeleid maken voor macOS-apparaten in Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Voorwaardelijke toegang in Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Network Policy Server-extensie voor Azure AD-Multi-Factor Authentication

**Type:** Nieuwe functie **service categorie:**  multi-factor Authentication- **product mogelijkheid:** gebruikers verificatie

Met de Network Policy Server extensie voor Azure AD Multi-Factor Authentication worden cloud-gebaseerde Multi-Factor Authentication mogelijkheden toegevoegd aan uw verificatie-infra structuur met behulp van uw bestaande servers. Met de uitbrei ding Network Policy Server kunt u een telefoon gesprek, tekst bericht of verificatie via de telefoon toevoegen aan uw bestaande verificatie stroom. U hoeft geen nieuwe servers te installeren, te configureren en te onderhouden.

Deze extensie is gemaakt voor organisaties die virtuele particuliere netwerk verbindingen willen beveiligen zonder de Azure-Multi-Factor Authentication-server te implementeren. De uitbrei ding Network Policy Server fungeert als een adapter tussen RADIUS-en Cloud-Azure AD-Multi-Factor Authentication om een tweede factor van verificatie te bieden voor federatieve of gesynchroniseerde gebruikers.

Zie [uw bestaande Network Policy Server-infra structuur integreren met Azure AD multi-factor Authentication](../authentication/howto-mfa-nps-extension.md)voor meer informatie.

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Verwijderde gebruikers herstellen of permanent verwijderen

**Type:** Nieuwe functie **service categorie:** gebruikers beheer **product mogelijkheid:** map

In het Azure AD-beheer centrum kunt u nu het volgende doen:

- Een verwijderde gebruiker herstellen.
- Een gebruiker definitief verwijderen.

**Om het uit te proberen:**

1. Selecteer in het Azure AD-beheer centrum [alle gebruikers](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) in de sectie **beheren** .

2. Selecteer **onlangs verwijderde gebruikers** in de lijst **weer geven** .

3. Selecteer een of meer recent verwijderde gebruikers en herstel deze vervolgens of verwijder ze definitief.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-app

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

De volgende apps zijn toegevoegd aan de lijst met [goedgekeurde client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft Planner
- Azure Information Protection

Zie voor meer informatie:

- [Vereiste voor client-app vereist](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>De besturings elementen ' OR ' gebruiken in een beleid voor voorwaardelijke toegang

**Type:** Gewijzigde functie **service categorie:** **product mogelijkheid** voor voorwaardelijke toegang: identiteits beveiliging en-beveiliging

U kunt nu ' of ' (een van de geselecteerde besturings elementen vereisen) voor besturings elementen voor voorwaardelijke toegang gebruiken. U kunt deze functie gebruiken voor het maken van beleids regels met ' OR ' tussen besturings elementen voor toegang. U kunt deze functie bijvoorbeeld gebruiken om een beleid te maken waarbij een gebruiker zich moet aanmelden met behulp van Multi-Factor Authentication ' of ' moet worden gebruikt voor een compatibel apparaat.

Zie [besturings elementen in voorwaardelijke toegang tot Azure AD](../conditional-access/controls.md)voor meer informatie.

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregatie van real-time risico detecties

**Type:** Gewijzigde functie **service categorie:** identiteits bescherming **product mogelijkheid:** identiteits beveiliging en-beveiliging

In Azure AD Identity Protection worden alle real-time-risico detecties die afkomstig zijn van hetzelfde IP-adres op een bepaalde dag, nu geaggregeerd voor elk type risico detectie. Met deze wijziging wordt het volume aan risico detecties beperkt zonder dat er wijzigingen in de gebruikers beveiliging worden weer gegeven.

De onderliggende realtime detectie werkt telkens wanneer de gebruiker zich aanmeldt. Als u een beveiligings beleid voor aanmeldings Risico's hebt ingesteld op Multi-Factor Authentication of toegang blokkeert, wordt het nog steeds geactiveerd tijdens elke Risk ante aanmelding.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afschaffen

**Type:** Planning voor **service categorie wijzigen:** rapportage van **product mogelijkheden:** Identity Lifecycle Management

De Azure Portal biedt u het volgende:

- Een nieuwe Azure AD-beheer console.
- Nieuwe Api's voor activiteiten-en beveiligings rapporten.

Als gevolg van deze nieuwe mogelijkheden werden de rapport-Api's onder het punt/Reports buiten gebruik gesteld op 10 december 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Detectie van automatisch aanmeldings veld

**Type:** Vaste **service categorie:** mijn apps **product mogelijkheid:** eenmalige aanmelding

Azure AD biedt ondersteuning voor het automatisch detecteren van aanmeldings velden voor toepassingen die een HTML-gebruikers naam en-wachtwoord veld weer geven. Deze stappen worden beschreven in [het automatisch vastleggen van aanmeldings velden voor een toepassing](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). U kunt deze mogelijkheid vinden door een *niet-galerie* toepassing toe te voegen op de pagina **bedrijfs toepassingen** in de [Azure Portal](https://aad.portal.azure.com). Daarnaast kunt u de modus voor **eenmalige aanmelding** op deze nieuwe toepassing configureren voor **eenmalige aanmelding op basis van wacht woorden**, een web-URL opgeven en de pagina vervolgens opslaan.

Deze functionaliteit is tijdelijk uitgeschakeld vanwege een service probleem. Het probleem is opgelost en de detectie van het automatische aanmeldings veld is weer beschikbaar.

---

### <a name="new-multi-factor-authentication-features"></a>Nieuwe Multi-Factor Authentication functies

**Type:** Nieuwe functie **service categorie:** multi-factor Authentication- **product mogelijkheid:** identiteits beveiliging en-beveiliging

Multi-factor Authentication (MFA) is een essentieel onderdeel van de beveiliging van uw organisatie. De volgende functies zijn toegevoegd om referenties meer adaptief te maken en de ervaring te versoepelen:

- Multi-factor Challenge-resultaten worden rechtstreeks geïntegreerd in het aanmeldings rapport van Azure AD, inclusief programmatische toegang tot MFA-resultaten.
- De configuratie van MFA is dieper geïntegreerd in de Azure AD-configuratie in de Azure Portal.

Met deze open bare preview is MFA-beheer en-rapportage een geïntegreerd onderdeel van de basis configuratie-ervaring van Azure AD. U kunt nu de functionaliteit van de MFA-beheer portal beheren binnen de Azure AD-ervaring.

Zie [referentie voor MFA-rapportage in de Azure Portal](../authentication/howto-mfa-reporting.md)voor meer informatie.

---

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

**Type:** Nieuwe functie **service categorie:** gebruiksvoorwaarden **product capaciteit:** naleving

U kunt Azure AD-gebruiks voorwaarden gebruiken om informatie te presen teren, zoals relevante disclaimers voor juridische of nalevings vereisten voor gebruikers.

U kunt de gebruiksrecht overeenkomst van Azure AD gebruiken in de volgende scenario's:

- Algemene Gebruiks voorwaarden voor alle gebruikers in uw organisatie
- Specifieke gebruiks voorwaarden op basis van de kenmerken van een gebruiker (bijvoorbeeld artsen vs. verpleegt of binnenland versus internationale werk nemers, uitgevoerd door dynamische groepen)
- Specifieke gebruiks voorwaarden voor toegang tot zakelijke apps met hoge impact, zoals Sales Force

Zie [gebruiks voorwaarden voor Azure AD](../conditional-access/terms-of-use.md)voor meer informatie.

---

### <a name="enhancements-to-privileged-identity-management"></a>Verbeteringen in Privileged Identity Management

**Type:** Nieuwe functie **service categorie:** privileged Identity Management **Product capaciteit:** privileged Identity Management

Met Azure AD Privileged Identity Management kunt u de toegang tot Azure-resources (preview) binnen uw organisatie beheren, controleren en bewaken tot:

- Abonnementen
- Resourcegroepen
- Virtuele machines

Alle resources in de Azure Portal die gebruikmaken van de functionaliteit van Azure RBAC, kunnen profiteren van alle functies voor beveiliging en levenscyclus beheer die Azure AD Privileged Identity Management te bieden hebben.

Zie [privileged Identity Management voor Azure-resources](../privileged-identity-management/azure-pim-resource-rbac.md)voor meer informatie.

---

### <a name="access-reviews"></a>Toegangsbeoordelingen

**Type:** Nieuwe functie **service categorie:** toegangs beoordelingen **product mogelijkheden:** naleving

Organisaties kunnen toegangs beoordelingen (preview) gebruiken om groepslid maatschappen en toegang tot bedrijfs toepassingen efficiënt te beheren:

- U kunt toegang voor gastgebruikers opnieuw certificeren door gebruik te maken van toegangsbeoordelingen van hun toegang tot toepassingen en lidmaatschappen van groepen. Revisoren kunnen efficiënt bepalen of gast toegang is toegestaan op basis van de inzichten van de toegangs Beoordelingen.
- Met toegangsbeoordelingen kunt u de toegang van werknemers voor toepassingen en groepslidmaatschappen opnieuw certificeren.

U kunt de controles van toegangsbeoordelingen verzamelen in programma's die relevant zijn voor uw organisatie, om beoordelingen bij te houden voor naleving of risicogevoelige toepassingen.

Zie [Azure AD Access revisies](../governance/access-reviews-overview.md)(Engelstalig) voor meer informatie.

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Toepassingen van derden verbergen vanuit mijn apps en het start programma voor apps van Office 365

**Type:** Nieuwe functie **service categorie:** mijn apps **product mogelijkheid:** eenmalige aanmelding

U kunt nu beter apps beheren die worden weer gegeven op de portals van uw gebruikers via een nieuwe eigenschap voor het verbergen van een **app** . U kunt apps verbergen om te helpen in gevallen waarin app-tegels worden weer gegeven voor back-end-services of dubbele tegels en de app-starters van onbelangrijke gebruikers. De wissel knop bevindt zich in de sectie **Eigenschappen** van de app van derden en is **zichtbaar voor de gebruiker?** U kunt een app ook via Power shell verbergen.

Zie [een toepassing van een derde partij verbergen in de gebruikers ervaring van Azure AD](../manage-apps/hide-application-from-user-portal.md)voor meer informatie.


**Wat is er beschikbaar?**

 Als onderdeel van de overgang naar de nieuwe beheer console zijn er twee nieuwe Api's voor het ophalen van Azure AD-activiteiten logboeken beschikbaar. De nieuwe set Api's biedt uitgebreide filter-en sorteer functies, naast het bieden van uitgebreide controle-en aanmeldings activiteiten. De gegevens die eerder via de beveiligings rapporten beschikbaar zijn, kunnen worden geopend via de API voor risico detectie van identiteits beveiliging in Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix voor Identity Manager

**Type:** Gewijzigde functie **service categorie:** Identity Manager- **product mogelijkheid:** Identity Lifecycle Management

Een hotfix-samengevouwen pakket (build 4.4.1642.0) is beschikbaar vanaf 25 september 2017 voor Identity Manager 2016 Service Pack 1. Dit samengevouwen pakket:

- Lost problemen op en voegt verbeteringen toe.
- Is een cumulatieve update waarbij alle Identity Manager 2016 Service Pack 1-updates worden vervangen om 4.4.1459.0 te bouwen voor Identity Manager 2016.
- Hiervoor moet u Identity Manager 2016 build 4.4.1302.0 hebben.

Zie hotfixcombinatiepakket [(build 4.4.1642.0) is beschikbaar voor Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562)voor meer informatie.

---
