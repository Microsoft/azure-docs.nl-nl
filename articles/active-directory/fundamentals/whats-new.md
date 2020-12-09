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
ms.date: 12/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 710e31c18338243f9405a071b8fa544fe44044be
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903153"
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
 
 [Aangepaste RBAC-rollen voor gedelegeerd beheer van bedrijfs toepassingen](../users-groups-roles/roles-custom-available-permissions.md) zijn nu beschikbaar als open bare preview. Deze nieuwe machtigingen zijn gebaseerd op de aangepaste rollen voor het beheer van app-registratie, waarmee nauw keurige controle kan worden uitgevoerd op de toegang die uw beheerders hebben. Na verloop van tijd worden extra machtigingen voor het delegeren van het beheer van Azure AD vrijgegeven.

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

### <a name="azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Azure AD-toepassingsproxy biedt systeem eigen ondersteuning voor eenmalige aanmelding voor toepassingen die gebruikmaken van headers voor verificatie

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

Zie [Gebruikers inrichten voor SaaS-toepassingen automatiseren met Azure AD](../manage-apps/user-provisioning.md)voor meer informatie.
 
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

Raadpleeg de [BitLocker Recovery API](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) voor updates in de documentatie om deze wijzigingen weer te geven.

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

Zie [externe identiteiten uitschakelen en verwijderen met Azure AD-toegangs beoordelingen (preview)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview)voor meer informatie over deze functie.
 
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

Het is raadzaam om in plaats van de weergave naam rollen sjabloon-Id's te gebruiken in hun Power shell-script en code. De sjabloon-ID van de functie wordt ondersteund voor gebruik in [directoryRoles](/graph/api/resources/directoryrole) -en [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) -objecten. Zie id van de [functie sjabloon](../roles/permissions-reference.md#role-template-ids)voor meer informatie over de id van de functie sjabloon.

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
 

 Alle verbonden organisaties hebben nu een extra eigenschap met de naam ' state '. Met de status wordt bepaald hoe de verbonden organisatie wordt gebruikt in beleids regels die verwijzen naar ' alle geconfigureerde verbonden organisaties '. De waarde is ' geconfigureerd ' (wat betekent dat de organisatie zich in het bereik van beleids regels bevindt dat de component ' all ' gebruikt) of ' voorgesteld ' (wat betekent dat de organisatie zich niet binnen het bereik bevindt).  

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

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity supertoe](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud integratie voor Azure](https://apps.mypurecloud.com/msteams-integration/), [zone technologieën Portal](https://portail.zonetechnologie.com/signin), [beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [afspraak herinnering](https://app.appointmentreminder.co.nz/account/login), [Cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [greetend](https://app.greetly.com/), [OrgVitality SSO} (. /SaaS-apps/orgvitality-SSO-tutorial.MD), [Web vracht lucht](../saas-apps/web-cargo-air-tutorial.md), [lus flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

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

    Met deze functie kunnen IT-beheerders gebruikers-, groeps-of contact kenmerken van AD toewijzen aan Azure AD met behulp van de verschillende toewijzings typen die vandaag beschikbaar zijn. Kenmerk toewijzing is een functie die wordt gebruikt voor het standaardiseren van de waarden van de kenmerken van Active Directory naar Azure Active Directory. Eén kan bepalen of de kenmerk waarde direct moet worden toegewezen aan de naam van AD naar Azure AD of dat expressies moeten worden gebruikt om de kenmerk waarden bij het inrichten van gebruikers te transformeren. [Meer informatie](../cloud-provisioning/how-to-attribute-mapping.md)

- Inrichting op aanvraag of gebruikers ervaring testen

    Wanneer u uw configuratie hebt ingesteld, wilt u wellicht testen of de gebruikers transformatie werkt zoals verwacht voordat u deze toepast op alle gebruikers binnen het bereik. Met inrichting op aanvraag kunnen IT-beheerders de DN (Distinguished Name) van een AD-gebruiker invoeren en zien of ze worden gesynchroniseerd zoals verwacht. Inrichting op aanvraag biedt een uitstekende manier om ervoor te zorgen dat de kenmerk toewijzingen die u eerder hebt uitgevoerd zoals verwacht. [Meer informatie](../cloud-provisioning/how-to-on-demand-provision.md)
 
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

## <a name="august-2020"></a>Augustus 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Updates voor Azure Multi-Factor Authentication-server-firewall vereisten

**Type:** Plan voor wijziging  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Vanaf 1 oktober 2020 zijn voor de Azure MFA Server-firewall vereisten extra IP-adresbereiken vereist.

Als u uitgaande firewall regels in uw organisatie hebt, werkt u de regels bij zodat uw MFA-servers kunnen communiceren met alle benodigde IP-bereiken. De IP-bereiken zijn gedocumenteerd in [vereisten voor Azure multi-factor Authentication-Server-firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Aanstaande wijzigingen in de gebruikers ervaring van de identiteit beveiligde Score

**Type:** Plan voor wijziging  
**Service categorie:** **Product mogelijkheid** voor identiteits beveiliging: identiteits beveiliging & beveiliging

De portal voor identiteits beveiliging wordt bijgewerkt met de wijzigingen die zijn aangebracht in de [nieuwe release](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)van de micro soft Secure Score. 

De preview-versie met de wijzigingen is aan het begin van september beschikbaar. De wijzigingen in de preview-versie zijn onder andere:
- De naam van de beveiligde score voor identiteits beveiliging is gewijzigd in ' beveiligde score voor identiteit ' voor een merk uitlijning met een beveiligde Score van micro soft
- Punten genormaliseerd naar standaard schaal en gerapporteerd in procenten in plaats van punten

In deze preview kunnen klanten scha kelen tussen de bestaande ervaring en de nieuwe ervaring. Deze preview gaat door tot eind november 2020. Na de preview worden de klanten automatisch omgeleid naar de nieuwe UX-ervaring.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nieuwe beperkte machtigingen voor gast toegang in azure AD-open bare preview

**Type:** Nieuwe functie  
**Service categorie:** Access Control   
**Product mogelijkheden:** Gebruikers beheer

Er zijn machtigingen op mapniveau bijgewerkt voor gast gebruikers. Met deze machtigingen kunnen beheerders aanvullende beperkingen en besturings elementen vereisen voor toegang tot externe gast gebruikers. Beheerders kunnen nu aanvullende beperkingen voor de toegang van externe gasten toevoegen aan het profiel en de lidmaatschaps gegevens van gebruikers en groepen. Met deze open bare preview-functie kunnen klanten externe gebruikers toegang op schaal beheren door groepslid maatschappen te maken, met inbegrip van het beperken van gast gebruikers om lidmaatschappen te zien van de groep (en) waarin ze zich bevinden.

Zie [beperkte gast toegangs machtigingen](../enterprise-users/users-restrict-guest-permissions.md) en [gebruikers standaard machtigingen](./users-default-permissions.md)voor meer informatie.
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Algemene Beschik baarheid van Delta query's voor service-principals

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Microsoft Graph Delta query ondersteunt nu het resource type in v 1.0:
- Service-principal

Clients kunnen nu op efficiënte wijze wijzigingen in deze bronnen bijhouden en de beste oplossing bieden voor het synchroniseren van wijzigingen aan deze resources met een lokale gegevens opslag. Zie [Delta query gebruiken om wijzigingen in Microsoft Graph gegevens](/graph/delta-query-overview)bij te houden voor informatie over het configureren van deze resources in een query.
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Algemene Beschik baarheid van Delta query's voor oAuth2PermissionGrant

**Type:** Nieuwe functie  
**Service categorie:** MS Graph  
**Product mogelijkheden:** Ontwikkelaars ervaring

Microsoft Graph Delta query ondersteunt nu het resource type in v 1.0:
- OAuth2PermissionGrant

Clients kunnen nu wijzigingen in deze bronnen bijhouden en de beste oplossing bieden voor het synchroniseren van wijzigingen aan deze resources met een lokale gegevens opslag. Zie [Delta query gebruiken om wijzigingen in Microsoft Graph gegevens](/graph/delta-query-overview)bij te houden voor informatie over het configureren van deze resources in een query.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-augustus 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden

In augustus 2020 zijn de volgende 25 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365-connector](http://enlyft.com/), [Serraview Space-gebruiks software oplossingen](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [zichtbaar](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum-Courseware-analyses: exact pad](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix naleving en vastleg ging](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight Enter prise Control platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec](https://www.clearance.network/), beschik [Wandera End User](https://www.wandera.com/) bare [ISAM](../saas-apps/isams-tutorial.md) [, VeraSMART,](../saas-apps/verasmart-tutorial.md) [Amiko,](https://amiko.web.rivero.app/) [twingate,](https://auth.twingate.com/signup) [trechter leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [Scalefusion](https://scalefusion.com/users/sign_in/) [Bpanda](https://goto.bpanda.com/login) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Voor het weer geven van uw toepassing in de app-galerie van Azure AD raadpleegt u de details hier https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resource forests nu beschikbaar voor Azure AD DS 

**Type:** Nieuwe functie **service categorie:** Azure AD Domain Services   
**Product mogelijkheden:** Azure AD Domain Services
 
De mogelijkheid van bron-forests in Azure AD Domain Services is nu algemeen beschikbaar. U kunt nu autorisatie inschakelen zonder wachtwoord hash-synchronisatie om Azure AD Domain Services te gebruiken, inclusief smartcard autorisatie. Zie voor meer informatie [replica sets-concepten en-functies voor Azure Active Directory Domain Services (preview)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regionale replica-ondersteuning voor Azure AD DS beheerde domeinen nu beschikbaar

**Type:** Nieuwe functie   
**Service categorie:** Azure AD Domain Services  
**Product mogelijkheden:** Azure AD Domain Services
 
U kunt een beheerd domein uitbreiden als u meer dan één replicaset per Azure AD-tenant wilt hebben. Replica sets kunnen worden toegevoegd aan elk gekoppeld virtueel netwerk in een Azure-regio die Azure AD Domain Services ondersteunt. Extra replica sets in verschillende Azure-regio's bieden geografisch herstel na noodgeval voor oudere toepassingen als een Azure-regio offline gaat. Zie voor meer informatie [replica sets-concepten en-functies voor Azure Active Directory Domain Services (preview)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Algemene Beschik baarheid van Azure AD My Sign-Ins

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Ervaringen van eind gebruikers
 
Azure AD My Sign-Ins is een nieuwe functie waarmee zakelijke gebruikers hun aanmeldings geschiedenis kunnen bekijken om te controleren of er ongebruikelijke activiteiten zijn. Daarnaast kunnen eind gebruikers met deze functie "This was ik" of "Ik ben ik" niet op verdachte activiteiten rapporteren. Zie [uw recente aanmeldings activiteit bekijken en doorzoeken op de pagina mijn Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)voor meer informatie over het gebruik van deze functie.
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Door SAP SuccessFactors HR gestuurde gebruikers inrichten voor Azure AD is nu algemeen verkrijgbaar

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
U kunt SAP SuccessFactors nu integreren als de gezaghebbende identiteits bron met Azure AD en de end-to-end identiteits levenscyclus automatiseren met HR-gebeurtenissen zoals nieuwe huur-en beëindigings functies voor het inrichten en het ongedaan maken van de inrichting van accounts in azure AD. 

Raadpleeg de zelf studie [Configure SAP SuccessFactors to Active Directory User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)voor meer informatie over het configureren van SAP SuccessFactors inkomend inrichten voor Azure AD.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Aangepaste open-ID Connect MS Graph API-ondersteuning voor Azure AD B2C

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Voorheen kunnen aangepaste open ID Connect-providers alleen worden toegevoegd of beheerd via de Azure Portal. De Azure AD B2C klanten kunnen nu ook de bèta versie van Microsoft Graph Api's toevoegen en beheren. Zie [Identity provider resource type](/graph/api/resources/identityprovider?view=graph-rest-beta)voor meer informatie over het configureren van deze bron met api's.
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Ingebouwde Azure AD-rollen toewijzen aan Cloud groepen

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control

U kunt nu Azure AD ingebouwde rollen toewijzen aan Cloud groepen met deze nieuwe functie. U kunt bijvoorbeeld de share point-beheerdersrol toewijzen aan Contoso_SharePoint_Admins groep. U kunt ook PIM gebruiken om de groep een in aanmerking komend lid van de rol te maken, in plaats van permanente toegang toe te kennen. Zie [Cloud groepen gebruiken om roltoewijzingen te beheren in azure Active Directory (preview)](../roles/groups-concept.md)voor meer informatie over het configureren van deze functie.
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>De ingebouwde rol voor Insights Business Leader is nu beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Gebruikers in de rol inzichten bedrijfs leider hebben toegang tot een set Dash boards en inzichten via de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Dit omvat volledige toegang tot alle Dash boards en weer gegeven inzicht in de functionaliteit voor het verkennen van gegevens. Gebruikers met deze rol hebben echter geen toegang tot de configuratie-instellingen van het product. Dit is de verantwoordelijkheid van de rol Insights-beheerder. Zie [machtigingen voor beheerdersrol in azure Active Directory](../roles/permissions-reference.md#insights-business-leader) voor meer informatie over deze rol.
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Ingebouwde rol Insights-beheerder is nu beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Gebruikers met de rol inzichten beheerder hebben toegang tot de volledige set beheer mogelijkheden in de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Een gebruiker met deze rol kan Directory gegevens lezen, service status controleren, bestands ondersteuning en toegang tot de aspecten van de Insights-beheerders instellingen. Zie [machtigingen voor beheerdersrol in azure Active Directory](../roles/permissions-reference.md#insights-administrator) voor meer informatie over deze rol.
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Toepassings beheerder en Cloud toepassings beheerder kunnen uitbreidings eigenschappen van toepassingen beheren

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
Voorheen kon alleen de globale beheerder de extensie- [eigenschap](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)beheren. Deze mogelijkheid wordt nu ook ingeschakeld voor de toepassings beheerder en de beheerder van de Cloud toepassing.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 hotfix 4.6.263.0 en connectors 1.1.1301.0

**Type:** Gewijzigde functie  
**Service categorie:** Microsoft Identity Manager  
**Product mogelijkheden:** Beheer van identiteits levenscyclus

Er is een hotfixcombinatiepakket [(build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Dit pakket bevat updates voor de MIM CM, MIM Synchronization Manager en PAM-onderdelen. Daarnaast bevat de 1.1.1301.0 van MIM generic-connectors updates voor de Graph connector.

---
 
## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Als IT-beheerder wil ik client-apps richten met behulp van voorwaardelijke toegang

**Type:** Plan voor wijziging   
**Service categorie:** Voorwaardelijke toegang  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
Met de GA-versie van de voor waarde client-apps in voorwaardelijke toegang wordt nieuw beleid nu standaard toegepast op alle client toepassingen. Dit omvat verouderde verificatie-clients. Bestaande beleids regels blijven ongewijzigd, maar de wissel knop *Ja/Nee* wordt verwijderd uit bestaande beleids regels om eenvoudig te zien welke client-apps door het beleid worden toegepast. 

Wanneer u een nieuw beleid maakt, moet u ervoor zorgen dat u gebruikers en service accounts uitsluit die nog steeds gebruikmaken van verouderde verificatie; Als u dit niet doet, worden ze geblokkeerd. [Meer informatie](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Aanstaande SCIM-nalevings correcties

**Type:** Plan voor wijziging  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
De Azure AD-inrichtings service maakt gebruik van de SCIM-standaard voor de integratie met toepassingen. Onze implementatie van de SCIM-standaard is in ontwikkeling en we verwachten dat we wijzigingen aanbrengen in het gedrag van het uitvoeren van PATCH bewerkingen en de eigenschap "actief" voor een resource instellen. [Meer informatie](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>De instelling groeps eigenaar op de Azure-beheer portal wordt gewijzigd

**Type:** Plan voor wijziging  
**Service categorie:** Groeps beheer  
**Product mogelijkheden:** Werking

De pagina instellingen van eigenaar van groepen kan worden geconfigureerd om machtigingen voor de toewijzing van de eigenaar te beperken tot een beperkte groep gebruikers in de Azure-beheer Portal en het toegangs venster. Binnenkort kunnen we de bevoegdheid groeps eigenaars toewijzen niet alleen op deze twee UX-portals, maar ook het beleid op de back-end afdwingen om consistent gedrag te bieden over eind punten, zoals Power shell en Microsoft Graph. 

We gaan de huidige instelling uitschakelen voor de klanten die deze niet gebruiken en bieden een optie om gebruikers in de komende maanden de bevoegdheid van de groeps eigenaars te bereiken. Zie voor hulp bij het bijwerken van groeps instellingen de groeps gegevens bewerken met behulp van [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>De ondersteuning voor Azure Active Directory registratie service wordt beëindigd voor TLS 1,0 en 1,1

**Type:** Plan voor wijziging  
**Service categorie:** Apparaatregistratie en-beheer  
**Product mogelijkheden:** Onafhankelijk

Trans port Layer Security (TLS) 1,2 en update servers en clients zullen binnenkort communiceren met Azure Active Directory Device Registration service. Ondersteuning voor TLS 1,0 en 1,1 voor communicatie met de Azure AD Device Registration-service wordt buiten gebruik stellen:
- Op 31 augustus 2020 in alle soevereine Clouds (GCC High, DoD, enz.)
- Op 30 oktober 2020 in alle commerciële Clouds

Meer [informatie](../devices/reference-device-registration-tls-1-2.md) over TLS 1,2 voor de Azure AD-registratie service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows hello voor bedrijven-aanmeldingen worden weer gegeven in Logboeken van Azure AD-aanmelding

**Type:** Vaste  
**Service categorie:** Rapporteren  
**Product mogelijkheden:** & rapportage controleren
 
Met Windows hello voor bedrijven kunnen eind gebruikers zich aanmelden bij Windows-computers met een penbeweging (zoals een pincode of biometrische). Azure AD-beheerders kunnen Windows hello voor bedrijven-aanmeldingen onderscheiden van andere Windows-aanmeldingen als onderdeel van de reis naar wacht woordloze verificatie. 

Beheerders kunnen nu zien of een Windows-verificatie gebruikt Windows hello voor bedrijven door het tabblad verificatie gegevens te controleren voor een Windows-aanmeld gebeurtenis op de Blade Azure AD Sign-Ins in de Azure Portal. Windows hello voor bedrijven-authenticaties bevatten ' WindowsHelloForBusiness ' in het veld verificatie methode. Raadpleeg de [documentatie bij aanmeld logboeken](../reports-monitoring/concept-sign-ins.md)voor meer informatie over het interpreteren van Sign-In Logboeken.
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Oplossingen voor het verwijderen van groepen en prestatie verbeteringen

**Type:** Vaste  
**Service categorie:** App-inrichting  
**Product mogelijkheden:** Beheer van identiteits levenscyclus
 
Voorheen werd het groeps object niet verwijderd wanneer een groep is gewijzigd van ' in-Scope ' naar ' out-of-scope ' en een beheerder op opnieuw starten heeft geklikt voordat de wijziging is voltooid. Het groeps object wordt nu verwijderd uit de doel toepassing wanneer deze buiten het bereik valt (uitgeschakeld, verwijderd, niet-toegewezen of het bereik filter niet heeft door gegeven). [Meer informatie](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Open bare Preview: beheerders kunnen nu aangepaste inhoud in het e-mail bericht toevoegen aan revisoren bij het maken van een toegangs beoordeling

**Type:** Nieuwe functie  
**Service categorie:** Toegangs beoordelingen  
**Product mogelijkheden:** Identity governance
 
Wanneer een nieuwe toegangs beoordeling wordt gemaakt, ontvangt de revisor een e-mail met de vraag om de toegangs beoordeling te volt ooien. Veel klanten hebben gevraagd om aangepaste inhoud toe te voegen aan het e-mail bericht, zoals contact gegevens of andere aanvullende ondersteunings inhoud om de revisor te begeleiden. 

Beheerders kunnen nu in open bare preview aangepaste inhoud opgeven in het e-mail bericht dat wordt verzonden naar revisors door inhoud toe te voegen aan de sectie ' Geavanceerd ' van Azure AD-toegangs Beoordelingen. Zie [een toegangs beoordeling van groepen en toepassingen maken in azure AD-toegangs beoordelingen](../governance/create-access-review.md)voor hulp bij het maken van toegangs Beoordelingen.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Autorisatie code stroom voor apps met één pagina beschikbaar

**Type:** Nieuwe functie  
**Service categorie:** Authenticaties (aanmeldingen)  
**Product mogelijkheden:** Ontwikkelaars ervaring
 
Vanwege de moderne Cookie beperkingen van derden, zoals Safari ITP, moet SPAs de autorisatie code stroom gebruiken in plaats van de impliciete stroom om SSO te onderhouden, en MSAL.js v 2. x wordt nu de autorisatie code stroom ondersteund. 

Er zijn overeenkomstige updates voor de Azure Portal, zodat u uw beveiligd-wachtwoord verificatie kunt bijwerken met het type Spa en de auth-code stroom moet gebruiken. Zie [gebruikers aanmelden en een toegangs token verkrijgen in een Java script-beveiligd-wachtwoord verificatie met behulp van de auth-code stroom](../develop/quickstart-v2-javascript-auth-code.md) voor verdere instructies.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD-toepassingsproxy ondersteunt nu de Extern bureaublad-services-webclient

**Type:** Nieuwe functie  
**Service categorie:** App-proxy  
**Product mogelijkheden:** Access Control

Azure AD-toepassingsproxy ondersteunt nu de Extern bureaublad-services (RDS)-webclient. Met de RDS-webclient kunnen gebruikers toegang krijgen tot Extern bureaublad-infra structuur via elke HTLM5 browser zoals micro soft Edge, Internet Explorer 11, Google Chrome, enzovoort. Gebruikers kunnen met externe apps of Bureau bladen werken, zoals bij een lokaal apparaat. Met behulp van Azure AD-toepassingsproxy kunt u de beveiliging van uw RDS-implementatie verbeteren door pre-authenticatie en beleid voor voorwaardelijke toegang af te dwingen voor alle typen uitgebreide client-apps. Zie [extern bureaublad publiceren met Azure AD-toepassingsproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)voor meer informatie.
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Volgende generatie Azure AD B2C gebruikers stromen in open bare preview

**Type:** Nieuwe functie  
**Service categorie:** B2C-Consumer Identity Management  
**Product mogelijkheden:** B2B/B2C
 
Dankzij de vereenvoudigde ervaring voor gebruikers stroom kunt u de functie pariteit gebruiken met preview-functies en is de start voor alle nieuwe functies. Gebruikers kunnen nieuwe functies binnen dezelfde gebruikers stroom inschakelen, waardoor er minder nood zaak is om meerdere versies te maken met elke nieuwe functie versie. Ten slotte vereenvoudigt de nieuwe gebruikers vriendelijke UX de selectie en het maken van gebruikers stromen. Probeer het nu door [een gebruikers stroom te maken](../../active-directory-b2c/tutorial-create-user-flows.md). 

Zie [gebruikers stroom versies in azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md)voor meer informatie over gebruikers stromen.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nieuwe federatieve apps die beschikbaar zijn in de Azure AD-toepassings galerie-juli 2020

**Type:** Nieuwe functie  
**Service categorie:** Zakelijke apps  
**Product capaciteit:** integratie van derden
 
In juli 2020 zijn de volgende 55 nieuwe toepassingen toegevoegd aan de app-galerie met Federatie ondersteuning:

[Clap Your handen](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor kluis](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect Connect](https://app.templafy.com/), [PeterConnects receptie](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [munten constructie Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [terugdraaien](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [communautair Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data hub eenmalige aanmelding](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [uitgang](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [rechter Cyber beveiliging ADI](https://right-hand.ai/), [Fyde Enter prise-verificatie](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [conrise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [inspireren](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML connector](https://www.strata.io/identity-fabric/), [Smartschool (school beheersysteem)](https://smartschoolz.com/login), [Zepto-intelligent keurige](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp Kemp](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack eenmalige aanmelding](../saas-apps/browserstack-single-sign-on-tutorial.md)

U kunt hier ook de documentatie van alle toepassingen vinden https://aka.ms/AppsTutorial

Lees de informatie hier om uw toepassing in de app-galerie van Azure AD weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nieuwe inrichtings connectors in de Azure AD-toepassings galerie-juli 2020

**Type:** Nieuwe functie  
**Service categorie:** App-inrichting  
**Product capaciteit:** integratie van derden

U kunt nu het maken, bijwerken en verwijderen van gebruikers accounts automatiseren voor de zojuist geïntegreerde app [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Voor meer informatie over hoe u uw organisatie beter kunt beveiligen met behulp van automatische toewijzing van gebruikers accounts, raadpleegt [u de gebruikers inrichting voor SaaS-toepassingen automatiseren met Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Roltoewijzingen weer geven voor alle bereiken en de mogelijkheid om deze te downloaden naar een CSV-bestand

**Type:** Gewijzigde functie  
**Service categorie:** Azure AD-rollen  
**Product mogelijkheden:** Access Control
 
U kunt nu roltoewijzingen weer geven voor alle bereiken voor een rol in het tabblad ' rollen en beheerders ' in de Azure AD-Portal. U kunt ook de roltoewijzingen voor elke rol naar een CSV-bestand downloaden. Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](../roles/manage-roles-portal.md)voor hulp bij het weer geven en toevoegen van roltoewijzingen.
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Afschaffing van Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Type:** Keur  
**Service categorie:** MFA  
**Product mogelijkheden:** Beveiliging van identiteits beveiliging &
 
De Azure Multi-Factor Authentication Software Development (Azure MFA SDK) heeft het einde van de levens cyclus bereikt op 14 november 2018, zoals het eerst werd aangekondigd in november 2017. Micro soft zal de SDK-Service vanaf 30 september 2020 afsluiten. Aanroepen van de SDK zullen mislukken.

Als uw organisatie de Azure MFA SDK gebruikt, moet u de migratie op 30 september 2020:
- Azure MFA SDK voor MIM: als u de SDK met MIM gebruikt, moet u migreren naar Azure MFA-server en Privileged Access Management (PAM) activeren volgens deze [instructies](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK voor aangepaste apps: overweeg uw app te integreren in azure AD en gebruik voorwaardelijke toegang om MFA af te dwingen. Bekijk deze [pagina](../manage-apps/plan-an-application-integration.md)om aan de slag te gaan. 

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
 
Er is een nieuwe gedelegeerde machtiging EntitlementManagement. Read. all is nu beschikbaar voor gebruik met de API voor rechten beheer in Microsoft Graph bèta. Zie [werken met de Azure AD-API voor rechten beheer](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)voor meer informatie over de beschik bare api's.

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
 
