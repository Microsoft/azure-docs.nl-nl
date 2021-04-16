---
title: Archiveren voor Wat is er nieuw in Azure Active Directory? | Microsoft Docs
description: De opmerkingen bij de release Wat is er nieuw in de sectie Overzicht van deze inhoudsset bevat 6 maanden aan activiteit. Na zes maanden worden de items verwijderd uit het hoofdartikel en in dit archiefartikel gezet.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7fda6345356568d512b396c412603cf7d837f7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532403"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiveren voor Wat is er nieuw in Azure Active Directory?

Het primaire artikel Wat is [er nieuw in Azure Active Directory?](whats-new.md) bevat updates voor de afgelopen zes maanden, terwijl dit artikel alle oudere informatie bevat.

Wat is er nieuw in Azure Active Directory? opmerkingen bij de release bieden informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - september 2020

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Openbare preview-versie van cloud inrichten

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD Cloud Provisioning **Product capability:** Identity Lifecycle Management
 
Azure AD Connect het vernieuwen van de openbare preview-versie van Cloud Provisioning zijn twee belangrijke verbeteringen die zijn ontwikkeld op basis van feedback van klanten: 

- Kenmerktoewijzingservaring via Azure Portal

    Met deze functie kunnen IT-beheerders kenmerken van gebruikers, groepen of contactpersonen van AD toewijzen aan Azure AD met behulp van verschillende toewijzingstypen die momenteel aanwezig zijn. Kenmerktoewijzing is een functie die wordt gebruikt voor het standaardiseren van de waarden van de kenmerken die van Active Directory naar de Azure Active Directory. U kunt bepalen of de kenmerkwaarde rechtstreeks moet worden toekend vanuit AD aan Azure AD of dat expressies moeten worden gebruikt om de kenmerkwaarden te transformeren bij het inrichten van gebruikers. [Meer informatie](../cloud-sync/how-to-attribute-mapping.md)

- Inrichting op aanvraag of gebruikerservaring testen

    Nadat u de configuratie hebt ingesteld, kunt u testen of de gebruikerstransformatie werkt zoals verwacht voordat u deze op alle gebruikers binnen het bereik gaat toepassen. Met inrichting op aanvraag kunnen IT-beheerders de DN (Distinguished Name) van een AD-gebruiker invoeren en zien of ze worden gesynchroniseerd zoals verwacht. Inrichting op aanvraag biedt een uitstekende manier om ervoor te zorgen dat de kenmerktoewijzingen die u eerder hebt gedaan, werken zoals verwacht. [Meer informatie](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>BitLocker-herstel gecontroleerd in Azure AD - openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** Beheer van apparaattoegang  
**Productmogelijkheid:** Levenscyclusbeheer van apparaten
 
Wanneer IT-beheerders of eindgebruikers BitLocker-herstelsleutels lezen waar ze toegang toe hebben, genereert Azure Active Directory nu een auditlogboek waarin wordt vast leggen wie toegang heeft tot de herstelsleutel. Dezelfde controle bevat details van het apparaat waar de BitLocker-sleutel aan is gekoppeld.

Eindgebruikers hebben toegang [tot hun herstelsleutels via Mijn account.](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) IT-beheerders hebben toegang tot herstelsleutels via de [BitLocker-herstelsleutel-API in de bètaversie](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) of via de Azure AD-portal. Zie [BitLocker-sleutels weergeven of kopiëren in de Azure AD-portal voor meer informatie.](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)

---

### <a name="teams-devices-administrator-built-in-role"></a>Ingebouwde rol Van De beheerder van Teams-apparaten

**Type:** Nieuwe functie  
**Servicecategorie:** Rbac  
**Productmogelijkheid:** Access Control
 
Gebruikers met de [rol Teams-apparaatbeheerder](../roles/permissions-reference.md#teams-devices-administrator) kunnen [door Teams gecertificeerde apparaten beheren](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) vanuit het Teams-beheercentrum. 

Met deze rol kan de gebruiker alle apparaten in één oogopslag weergeven, met de mogelijkheid om apparaten te zoeken en te filteren. De gebruiker kan ook de details van elk apparaat controleren, inclusief het aangemelde account en het make en model van het apparaat. De gebruiker kan de instellingen op het apparaat wijzigen en de softwareversies bijwerken. Deze rol verleent geen machtigingen om de teamsactiviteit te controleren en de kwaliteit van het apparaat aan te roepen.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Geavanceerde querymogelijkheden voor mapobjecten

**Type:** Nieuwe functie  
**Servicecategorie:** MS Graph  
**Productmogelijkheid:** Ontwikkelaarservaring
 
Alle nieuwe querymogelijkheden die zijn geïntroduceerd voor Directory Objects in Azure AD API's zijn nu beschikbaar in het v1.0-eindpunt en zijn gereed voor productie. Ontwikkelaars kunnen mapobjecten en gerelateerde koppelingen tellen, zoeken, filteren en sorteren met behulp van de standaard OData-operators.

Zie de documentatie hier voor meer [informatie](https://aka.ms/BlogPostMezzoGA)en u kunt ook feedback verzenden met deze [korte enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Openbare preview: continue toegangsevaluatie voor tenants die beleid voor voorwaardelijke toegang hebben geconfigureerd

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Identity Security & Protection
 
Continue toegangsevaluatie (CAE) is nu beschikbaar in openbare preview voor Azure AD-tenants met beleid voor voorwaardelijke toegang. Met CAE worden kritieke beveiligingsgebeurtenissen en -beleidsregels in realtime geëvalueerd. Dit omvat account uitschakelen, wachtwoord opnieuw instellen en locatie wijzigen. Zie Continue [toegangsevaluatie voor meer informatie.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Openbare preview: gebruikers die een toegangspakket aanvragen, aanvullende vragen stellen om goedkeuringsbeslissingen te verbeteren

**Type:** Nieuwe functie  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 
Beheerders kunnen nu vereisen dat gebruikers die een toegangspakket aanvragen aanvullende vragen beantwoorden die verder gaan dan alleen zakelijke redenen in de azure AD-Mijn toegang portal. De antwoorden van de gebruikers worden vervolgens weergegeven aan de goedkeurders om ze te helpen een nauwkeurigere goedkeuringsbeslissing voor toegang te maken. Zie Aanvullende aanvraaggegevens verzamelen voor goedkeuring [(preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)voor meer informatie.
 
---

### <a name="public-preview-enhanced-user-management"></a>Openbare preview: Uitgebreid gebruikersbeheer

**Type:** Nieuwe functie  
**Servicecategorie:** Gebruikersbeheer  
**Productmogelijkheid:** Gebruikersbeheer
 

De Azure AD-portal is bijgewerkt zodat u gemakkelijker gebruikers kunt vinden op de pagina's Alle gebruikers en Verwijderde gebruikers. Wijzigingen in de preview zijn onder andere: 
- Meer zichtbare gebruikerseigenschappen, waaronder object-id, synchronisatiestatus van map, aanmaaktype en id-vergever.
- Met Zoeken kunnen nu gecombineerde namen, e-mailberichten en object-ID's worden doorzocht.
- Verbeterde filtering op gebruikerstype (lid, gast en geen), synchronisatiestatus van directory, aanmaaktype, bedrijfsnaam en domeinnaam.
- Nieuwe sorteermogelijkheden voor eigenschappen zoals naam, user principal name en verwijderingsdatum.
- Een nieuw totaal aantal gebruikers dat wordt bijgewerkt met zoekopdrachten of filters.

Zie Verbeteringen in gebruikersbeheer [(preview) in](../enterprise-users/users-search-enhanced.md)Azure Active Directory voor meer Azure Active Directory.

---

### <a name="new-notes-field-for-enterprise-applications"></a>Veld Nieuwe notities voor Bedrijfstoepassingen

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps **Product capability:** SSO

U kunt notities in vrije tekst toevoegen aan Bedrijfstoepassingen. U kunt alle relevante informatie toevoegen die u helpt bij het manageren van toepassingen onder Bedrijfstoepassingen. Zie Quickstart: Eigenschappen configureren voor een toepassing in uw Azure Active Directory [(Azure AD)-tenant](../manage-apps/add-application-portal-configure.md)voor meer informatie. 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - september 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden

In september 2020 hebben we de volgende 34 nieuwe toepassingen toegevoegd in onze App-galerie met ondersteuning voor federatie:

[VMware Horizon - Unified Access Gateway](), Pulse Secure [PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md) [,GramleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender Fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & Access Management](https://my.priva.com/), Nitro [Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Secured Signing Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec Online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen: https://aka.ms/AppsTutorial .

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nieuwe delegeringsrol in Azure AD-rechtenbeheer: Toegangspakkettoewijzingsmanager

**Type:** Nieuwe functie  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 
Er is een nieuwe rol Toegangspakkettoewijzingsbeheer toegevoegd in Azure AD-rechtenbeheer om gedetailleerde machtigingen te bieden voor het beheren van toewijzingen. U kunt nu taken delegeren aan een gebruiker in deze rol, die toewijzingsbeheer van een toegangspakket kan delegeren aan een bedrijfseigenaar. Toegangspakkettoewijzingsbeheer kan echter niet het toegangspakketbeleid of andere eigenschappen wijzigen die door de beheerders zijn ingesteld. 

Met deze nieuwe rol profiteert u van de minste bevoegdheden die nodig zijn om het beheer van toewijzingen te delegeren en beheerbeheer te behouden voor alle andere configuraties van het toegangspakket. Zie Rechtenbeheerrollen [voor meer informatie.](../governance/entitlement-management-delegate.md#entitlement-management-roles)
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Wijzigingen in Privileged Identity Management onboardingstroom van een Privileged Identity Management

**Type:** Functie gewijzigd  
**Servicecategorie:** Privileged Identity Management  
**Productmogelijkheid:** Privileged Identity Management
 
Voorheen vereiste onboarding voor Privileged Identity Management (PIM) toestemming van de gebruiker en een onboarding-stroom op de blade van PIM met inschrijving in Azure AD MFA. Met de recente integratie van PIM-ervaring in de blade Azure AD-rollen en -beheerders wordt deze ervaring verwijderd. Elke tenant met een geldige P2-licentie wordt automatisch onboarding naar PIM.

Onboarding naar PIM heeft geen direct nadelig effect op uw tenant. U kunt de volgende wijzigingen verwachten:
- Aanvullende toewijzingsopties, zoals actief versus in aanmerking komend met begin- en eindtijd wanneer u een toewijzing maakt op de blade PIM- of Azure AD-rollen en -beheerders. 
- Aanvullende bereikmechanismen, zoals beheereenheden en aangepaste rollen, worden rechtstreeks in de toewijzingservaring geïntroduceerd. 
- Als u een globale beheerder of beheerder met bevoorrechte rol bent, kunt u enkele extra e-mailberichten ontvangen, zoals de wekelijkse samenvatting van PIM. 
- Mogelijk ziet u ook de service-principal ms-pim in het auditlogboek met betrekking tot roltoewijzing. Deze verwachte wijziging heeft geen invloed op uw normale werkstroom.

 Zie Beginnen met het gebruik van Privileged Identity Management voor [meer informatie.](../privileged-identity-management/pim-getting-started.md)

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-rechtenbeheer: in het deelvenster Selecteren van toegangspakketbronnen worden nu standaard de resources weergegeven die momenteel in de geselecteerde catalogus staan

**Type:** Functie gewijzigd  
**Servicecategorie:** Beheer van gebruikerstoegang  
**Productmogelijkheid:** Rechtenbeheer
 

In de stroom voor het maken van toegangspakketten op het tabblad Resourcerollen verandert het gedrag van het deelvenster Selecteren. Op dit moment is het standaardgedrag om alle resources weer te geven die eigendom zijn van de gebruiker en resources die zijn toegevoegd aan de geselecteerde catalogus. 

Deze ervaring wordt gewijzigd zodat standaard alleen de resources worden weergegeven die momenteel in de catalogus zijn toegevoegd, zodat gebruikers eenvoudig resources uit de catalogus kunnen kiezen. De update helpt bij de detecteerbaarheid van de resources die kunnen worden toegevoegd aan toegangspakketten en vermindert het risico dat per ongeluk resources worden toegevoegd die eigendom zijn van de gebruiker die geen deel uitmaken van de catalogus. Zie Een nieuw toegangspakket [maken in Azure AD-rechtenbeheer voor meer informatie.](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>Augustus 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Updates voor firewallvereisten voor Azure Multi-Factor Authentication-server

**Type:** Plannen voor wijziging  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 
Vanaf 1 oktober 2020 Azure MFA-server firewallvereisten extra IP-adresbereiken nodig.

Als u uitgaande firewallregels in uw organisatie hebt, moet u de regels bijwerken zodat uw MFA-servers kunnen communiceren met alle benodigde IP-adresbereiken. De IP-adresbereiken worden beschreven in [Azure Multi-Factor Authentication-server firewallvereisten](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Toekomstige wijzigingen in de gebruikerservaring in Identity Secure Score

**Type:** Plannen voor wijziging  
**Servicecategorie:** Identity **Protection-productmogelijkheid:** Identity Security & Protection

We werken de portal voor de identiteits-beveiligingsscore bij om overeen te komen met de wijzigingen die zijn geïntroduceerd in de nieuwe versie van Microsoft Secure [Score.](/microsoft-365/security/mtp/microsoft-secure-score-whats-new) 

De preview-versie met de wijzigingen is vanaf begin september beschikbaar. De wijzigingen in de preview-versie zijn onder andere:
- Naam van 'Id-beveiligingsscore' gewijzigd in 'Beveiligingsscore voor identiteit' voor merkuitlijning met Microsoft-beveiligingsscore
- Wijst genormaliseerd naar standaardschaal en gerapporteerd in percentages in plaats van punten

In deze preview kunnen klanten schakelen tussen de bestaande ervaring en de nieuwe ervaring. Deze preview duurt tot eind november 2020. Na de preview worden de klanten automatisch omgeleid naar de nieuwe UX-ervaring.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nieuwe beperkte gasttoegangmachtigingen in Azure AD - openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** Access Control   
**Productmogelijkheid:** Gebruikersbeheer

We hebben machtigingen op mapniveau voor gastgebruikers bijgewerkt. Met deze machtigingen kunnen beheerders aanvullende beperkingen en besturingselementen voor toegang van externe gastgebruikers vereisen. Beheerders kunnen nu aanvullende beperkingen toevoegen voor de toegang van externe gasten tot profiel- en lidmaatschapsgegevens van gebruikers en groepen. Met deze openbare preview-functie kunnen klanten toegang van externe gebruikers op schaal beheren door groepslidmaatschap te ontzeggen, waaronder het voorkomen dat gastgebruikers lidmaatschappen zien van de groep(en) waarin ze zich in zich hebben.

Zie Beperkte machtigingen voor [gasttoegang en](../enterprise-users/users-restrict-guest-permissions.md) Standaardmachtigingen voor [gebruikers voor meer informatie.](./users-default-permissions.md)
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Algemene beschikbaarheid van Delta-query's voor service-principals

**Type:** Nieuwe functie  
**Servicecategorie:** MS Graph  
**Productmogelijkheid:** Ontwikkelaarservaring
 
Microsoft Graph Delta-query ondersteunt nu het resourcetype in v1.0:
- Service-principal

Clients kunnen wijzigingen in deze resources nu efficiënt bijhouden en bieden de beste oplossing om wijzigingen in deze resources te synchroniseren met een lokaal gegevensopslag. Zie Delta-query gebruiken om wijzigingen in de gegevens bij te houden voor meer informatie over het configureren van deze resources [in Microsoft Graph query.](/graph/delta-query-overview)
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Algemene beschikbaarheid van Delta-query's voor oAuth2PermissionGrant

**Type:** Nieuwe functie  
**Servicecategorie:** MS Graph  
**Productmogelijkheid:** Ontwikkelaarservaring

Microsoft Graph Delta-query ondersteunt nu het resourcetype in v1.0:
- OAuth2PermissionGrant

Clients kunnen wijzigingen in deze resources nu efficiënt bijhouden en bieden de beste oplossing om wijzigingen in deze resources te synchroniseren met een lokaal gegevensopslag. Zie Delta-query gebruiken om wijzigingen in de gegevens bij te houden voor meer informatie over het configureren van deze resources [in Microsoft Graph query.](/graph/delta-query-overview)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - augustus 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden

In augustus 2020 hebben we de volgende 25 nieuwe toepassingen toegevoegd in onze App-galerie met ondersteuning voor federatie:

[Backup365,](https://portal.backup365.io/login) [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Sis SIS](https://almau.getalma.com/), [Enlyft Dynamics 365 Connector,](http://enlyft.com/) [Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq](https://web.uniq.app/), [Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec Funnel](https://www.clearance.network/), [iSAMS](../saas-apps/isams-tutorial.md), [VeraSMART](../saas-apps/verasmart-tutorial.md), [Amaccount](https://amiko.web.rivero.app/), [Twingate](https://auth.twingate.com/signup), [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), Scalegrafie , [Bpanda](https://scalefusion.com/users/sign_in/), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Wandera End User](https://www.wandera.com/) [](https://goto.bpanda.com/login)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resource forests nu beschikbaar voor Azure AD DS 

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services   
**Productmogelijkheid:** Azure AD Domain Services
 
De mogelijkheid van resource forests in Azure AD Domain Services is nu algemeen beschikbaar. U kunt nu autorisatie zonder wachtwoordhashsynchronisatie inschakelen om Azure AD Domain Services te gebruiken, inclusief smartcardautorisatie. Zie Concepten en functies van [replicasets voor Azure Active Directory Domain Services (preview) voor meer informatie.](../../active-directory-domain-services/concepts-replica-sets.md)
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regionale replicaondersteuning voor Azure AD DS beheerde domeinen nu beschikbaar

**Type:** Nieuwe functie   
**Servicecategorie:** Azure AD Domain Services  
**Productmogelijkheid:** Azure AD Domain Services
 
U kunt een beheerd domein uitbreiden als u meer dan één replicaset per Azure AD-tenant wilt hebben. Replicasets kunnen worden toegevoegd aan elk peered virtueel netwerk in elke Azure-regio die ondersteuning biedt Azure AD Domain Services. Extra replica sets in verschillende Azure-regio's bieden geografisch herstel na noodgeval voor oudere toepassingen als een Azure-regio offline gaat. Zie Concepten en functies van [replicasets voor Azure Active Directory Domain Services (preview) voor meer informatie.](../../active-directory-domain-services/concepts-replica-sets.md)

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Algemene beschikbaarheid van Azure AD Mijn Sign-Ins

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
Azure AD Mijn Sign-Ins is een nieuwe functie waarmee zakelijke gebruikers hun aanmeldingsgeschiedenis kunnen controleren om te controleren op ongebruikelijke activiteiten. Daarnaast kunnen eindgebruikers met deze functie rapporteren over verdachte activiteiten: 'Dit was ik niet' of 'Dit was ik'. Zie Uw recente aanmeldingsactiviteiten weergeven en doorzoeken op de pagina Mijn Sign-Ins voor meer informatie over het gebruik [van deze functie.](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Sap SuccessFactors HR-gestuurde gebruikers inrichten in Azure AD is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
U kunt SAP SuccessFactors nu integreren als gezaghebbende identiteitsbron met Azure AD en de end-to-end identiteitslevenscyclus automatiseren met behulp van HR-gebeurtenissen zoals nieuwe medewerkers en beëindigingen om het inrichten en de inrichting van accounts in Azure AD te verbeteren. 

Raadpleeg de zelfstudie SAP SuccessFactors configureren voor het inrichten van Active Directory-gebruikers voor meer informatie over het configureren van binnenkomende inrichting van [SAP SuccessFactors naar](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)Azure AD.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Custom Open ID Connect MS Graph API ondersteuning voor Azure AD B2C

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
Voorheen konden aangepaste Open ID Connect-providers alleen worden toegevoegd of beheerd via de Azure Portal. Klanten kunnen Azure AD B2C nu ook toevoegen en beheren via Microsoft Graph bètaversie van API's. Zie identityProvider resource type voor meer informatie over het configureren van deze resource met [API's.](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Ingebouwde Azure AD-rollen toewijzen aan cloudgroepen

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-rollen  
**Productmogelijkheid:** Access Control

U kunt nu ingebouwde Azure AD-rollen toewijzen aan cloudgroepen met deze nieuwe functie. U kunt bijvoorbeeld de SharePoint-beheerdersrol toewijzen aan Contoso_SharePoint_Admins groep. U kunt PIM ook gebruiken om van de groep een in aanmerking komend lid van de rol te maken, in plaats van permanente toegang te verlenen. Zie Cloudgroepen gebruiken om roltoewijzingen te beheren in Azure Active Directory (preview) voor meer informatie [over het configureren van deze functie.](../roles/groups-concept.md)
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>De ingebouwde rol Insights Business Leader is nu beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-rollen  
**Productmogelijkheid:** Access Control
 
Gebruikers met de rol Business Leader van Insights hebben toegang tot een set dashboards en inzichten via de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Dit omvat volledige toegang tot alle dashboards en functionaliteit voor inzichten en gegevensverkenning. Gebruikers met deze rol hebben echter geen toegang tot de configuratie-instellingen van het product. Dit is de verantwoordelijkheid van de rol Insights-beheerder. Zie Machtigingen voor beheerdersrol in Azure Active Directory [](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>De ingebouwde rol Insights Administrator is nu beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-rollen  
**Productmogelijkheid:** Access Control
 
Gebruikers met de rol Insights-beheerder hebben toegang tot de volledige set beheermogelijkheden in de [M365 Insights-toepassing](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Een gebruiker met deze rol kan mapgegevens lezen, de service health bewaken, ondersteuningstickets voor bestanden en toegang krijgen tot de instellingenaspecten van de Insights-beheerder. Zie Machtigingen voor beheerdersrol in Azure Active Directory [](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Toepassingsbeheerder en cloudtoepassingsbeheerder kunnen extensie-eigenschappen van toepassingen beheren

**Type:** Functie gewijzigd  
**Servicecategorie:** Azure AD-rollen  
**Productmogelijkheid:** Access Control
 
Voorheen kon alleen de globale beheerder de [extensie-eigenschap beheren.](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true) We maken deze mogelijkheid nu ook mogelijk voor de toepassingsbeheerder en cloudtoepassingsbeheerder.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2-hotfix 4.6.263.0 en connectors 1.1.1301.0

**Type:** Functie gewijzigd  
**Servicecategorie:** Microsoft Identity Manager  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit

Er is [een hotfixpakket (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Dit samentelpakket bevat updates voor de onderdelen MIM CM, MIM Synchronization Manager en PAM. Daarnaast bevat de algemene MIM-connectors build 1.1.1301.0 updates voor de Graph-connector.

---

## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Als IT-beheerder wil ik me richten op client-apps die gebruikmaken van voorwaardelijke toegang

**Type:** Plannen voor wijziging   
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection
 
Met de ga-release van de voorwaarde voor client-apps in Voorwaardelijke toegang, worden nieuwe beleidsregels nu standaard toegepast op alle clienttoepassingen. Dit omvat verouderde verificatie-clients. Bestaande beleidsregels blijven ongewijzigd, maar de schakelknop *Ja/Nee* configureren wordt verwijderd uit bestaande beleidsregels om gemakkelijk te zien op welke client-apps het beleid van toepassing is. 

Wanneer u een nieuw beleid maakt, moet u ervoor zorgen dat u gebruikers en serviceaccounts uitsluit die nog gebruikmaken van verouderde verificatie; Als u dit niet hebt, worden ze geblokkeerd. [Meer informatie](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Toekomstige scIM-nalevingsfixes

**Type:** Plannen voor wijziging  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Identity Lifecycle Management
 
De Azure AD-inrichtingsservice maakt gebruik van de SCIM-standaard voor integratie met toepassingen. Onze implementatie van de SCIM-standaard is in ontwikkeling en we verwachten wijzigingen aan te brengen in ons gedrag met de manier waarop patchbewerkingen worden uitgevoerd en om de eigenschap 'actief' in te stellen op een resource. [Meer informatie](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>De instelling Groepseigenaar in de Azure-beheerportal wordt gewijzigd

**Type:** Plannen voor wijziging  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheid:** Samenwerking

Op de pagina Algemene instellingen voor groepen kunnen eigenaarsinstellingen worden geconfigureerd om de toewijzingsbevoegdheden voor eigenaars te beperken tot een beperkte groep gebruikers in de Azure-beheerportal en Toegangsvenster. Binnenkort kunnen we niet alleen bevoegdheden voor groepseigenaars toewijzen op deze twee UX-portals, maar ook het beleid op de back-end afdwingen om consistent gedrag te bieden voor eindpunten, zoals PowerShell en Microsoft Graph. 

We gaan de huidige instelling uitschakelen voor de klanten die deze niet gebruiken en bieden een optie om gebruikers in de komende maanden toegangsrechten voor groepseigenaars te geven. Zie Uw groepsgegevens bewerken met behulp van Azure Active Directory voor [hulp bij het bijwerken van Azure Active Directory.](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory Registration Service stopt de ondersteuning voor TLS 1.0 en 1.1

**Type:** Plannen voor wijziging  
**Servicecategorie:** Apparaatregistratie en -beheer  
**Productmogelijkheid:** Platform

Transport Layer Security (TLS) 1.2 en updateservers en -clients communiceren binnenkort met Azure Active Directory Device Registration Service. Ondersteuning voor TLS 1.0 en 1.1 voor communicatie met de Azure AD Device Registration-service wordt ingetrokken:
- Op 31 augustus 2020 in alle onafhankelijke clouds (GCC High, DoD, enzovoort)
- Op 30 oktober 2020 in alle commerciële clouds

[Meer informatie](../devices/reference-device-registration-tls-1-2.md) over TLS 1.2 voor de Azure AD-registratieservice.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello voor Bedrijven aanmeldingen zichtbaar in Azure AD-aanmeldingslogboeken

**Type:** Vaste  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle van & rapportage
 
Windows Hello voor Bedrijven kunnen eindgebruikers zich met een gebaar (zoals een pincode of biometrische gegevens) aanmelden bij Windows-computers. Azure AD-beheerders willen mogelijk onderscheid Windows Hello voor Bedrijven aanmeldingen van andere Windows-aanmeldingen als onderdeel van de reis van een organisatie naar verificatie zonder wachtwoord. 

Beheerders kunnen nu zien of een Windows-verificatie die wordt gebruikt Windows Hello voor Bedrijven, het tabblad Verificatiedetails voor een Windows-aanmeldingsgebeurtenis controleert op de blade Azure AD Sign-Ins in de Azure Portal. Windows Hello voor Bedrijven verificaties bevat 'WindowsHelloForBusiness' in het veld Verificatiemethode. Zie de documentatie voor aanmeldingslogboeken voor Sign-In informatie over het interpreteren [van logboeken.](../reports-monitoring/concept-sign-ins.md)
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Oplossingen voor groeps verwijderingsgedrag en prestatieverbeteringen

**Type:** Vaste  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
Voorheen, toen een groep werd gewijzigd van 'binnen bereik' in 'buiten het bereik' en een beheerder op opnieuw opstarten klikte voordat de wijziging werd voltooid, werd het groepsobject niet verwijderd. Het groepsobject wordt nu verwijderd uit de doeltoepassing wanneer het buiten het bereik valt (uitgeschakeld, verwijderd, niet toegewezen of niet geslaagd voor bereikfilter). [Meer informatie](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Openbare preview: Beheerders kunnen nu aangepaste inhoud in het e-mailbericht toevoegen aan revisoren bij het maken van een toegangsbeoordeling

**Type:** Nieuwe functie  
**Servicecategorie:** Toegangsbeoordelingen  
**Productmogelijkheid:** Identiteitsbeheer
 
Wanneer er een nieuwe toegangsbeoordeling wordt gemaakt, ontvangt de revisor een e-mail waarin hij/zij wordt gevraagd de toegangsbeoordeling te voltooien. Veel van onze klanten hebben gevraagd om de mogelijkheid om aangepaste inhoud toe te voegen aan het e-mailbericht, zoals contactgegevens of andere aanvullende ondersteunende inhoud om de revisor te begeleiden. 

Beheerders zijn nu beschikbaar in openbare preview en kunnen aangepaste inhoud opgeven in de e-mail die naar revisoren wordt verzonden door inhoud toe te voegen in de sectie 'geavanceerd' van Azure AD-toegangsbeoordelingen. Zie Een toegangsbeoordeling maken van groepen en toepassingen in Azure AD-toegangsbeoordelingen voor hulp bij het [maken van toegangsbeoordelingen.](../governance/create-access-review.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Autorisatiecodestroom voor apps met één pagina beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Ontwikkelaarservaring
 
Vanwege moderne cookiebeperkingen van derden, zoals Safari ITP, moeten SPA's de autorisatiecodestroom gebruiken in plaats van de impliciete stroom om eenmalige aanmelding te onderhouden. MSAL.js v 2.x ondersteunt nu de autorisatiecodestroom. 

Er zijn bijbehorende updates voor de Azure Portal zodat u kunt uw spa bijwerken naar type "spa" en de auth code stroom gebruiken. Zie Gebruikers aanmelden en een toegangsteken in een JavaScript-spa krijgen met behulp van de [auth-codestroom](../develop/quickstart-v2-javascript-auth-code.md) voor meer informatie.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD toepassingsproxy ondersteunt nu de Extern bureaublad-services-webclient

**Type:** Nieuwe functie  
**Servicecategorie:** App-proxy  
**Productmogelijkheid:** Access Control

Azure AD toepassingsproxy ondersteunt nu de Extern bureaublad-services (RDS)-webclient. Met de RDS-webclient kunnen gebruikers toegang krijgen tot Extern bureaublad-infrastructuur via elke voor HTLM5 geschikte browser, zoals Microsoft Edge, Internet Explorer 11, Google Chrome, enzovoort. Gebruikers kunnen vanaf elke locatie met externe apps of bureaubladen werken zoals met een lokaal apparaat. Met behulp van Azure AD toepassingsproxy u de beveiliging van uw RDS-implementatie verbeteren door pre-verificatie en beleid voor voorwaardelijke toegang af te afdwingen voor alle typen uitgebreide client-apps. Zie Publish Extern bureaublad with Azure AD toepassingsproxy (Publiceren [met Azure AD-toepassingsproxy).](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Gebruikersstromen Azure AD B2C de volgende generatie in openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
Vereenvoudigde gebruikersstroomervaring biedt functiepariteit met preview-functies en is de start voor alle nieuwe functies. Gebruikers kunnen nieuwe functies inschakelen binnen dezelfde gebruikersstroom, waardoor het minder nodig is om meerdere versies te maken bij elke nieuwe functiere release. Ten laatste vereenvoudigt de nieuwe, gebruiksvriendelijke UX de selectie en het maken van gebruikersstromen. Probeer het nu door [een gebruikersstroom te maken.](../../active-directory-b2c/tutorial-create-user-flows.md) 

Zie Gebruikersstroomversies in Azure Active Directory B2C voor [meer informatie over gebruikersstromen.](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - juli 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In juli 2020 hebben we de volgende 55 nieuwe toepassingen toegevoegd aan onze app-galerie met ondersteuning voor federatie:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beeuze](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnectsfree](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Tempom](https://start.cocoom.com/), [CONSTRUCTION Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [Rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign,](../saas-apps/cloudsign-tutorial.md) [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Egress](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (School Management System)](https://smartschoolz.com/login), [Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

U vindt hier ook de documentatie van alle toepassingen https://aka.ms/AppsTutorial

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Roltoewijzingen voor alle scopes weergeven en de mogelijkheid om ze te downloaden naar een CSV-bestand

**Type:** Functie gewijzigd  
**Servicecategorie:** Azure AD-rollen  
**Productmogelijkheid:** Access Control
 
U kunt nu roltoewijzingen voor alle scopes voor een rol weergeven op het tabblad Rollen en beheerders in de Azure AD-portal. U kunt deze roltoewijzingen ook voor elke rol downloaden naar een CSV-bestand. Zie Beheerdersrollen weergeven en toewijzen in Azure Active Directory voor hulp bij het weergeven en toevoegen [van roltoewijzingen.](../roles/manage-roles-portal.md)
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Afschaffing van Azure Multi-Factor Authentication-softwareontwikkeling (Azure MFA SDK)

**Type:** Afgekeurd  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 
De Azure Multi-Factor Authentication Software Development (Azure MFA SDK) heeft op 14 november 2018 het einde van de levensduur bereikt, zoals voor het eerst aangekondigd in november 2017. Microsoft sluit de SDK-service op 30 september 2020 af. Aanroepen naar de SDK mislukken.

Als uw organisatie de Azure MFA SDK gebruikt, moet u migreren voor 30 september 2020:
- Azure MFA SDK voor MIM: als u de SDK met MIM gebruikt, moet u migreren naar Azure MFA-server en Privileged Access Management (PAM) activeren volgens deze [instructies.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Azure MFA SDK voor aangepaste apps: U kunt uw app integreren in Azure AD en voorwaardelijke toegang gebruiken om MFA af te dwingen. Bekijk deze pagina om aan de slag [te gaan.](../manage-apps/plan-an-application-integration.md) 

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Gebruikersrisicovoorwaarde in beleid voor voorwaardelijke toegang

**Type:** Plannen voor wijziging  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection
 

Met ondersteuning voor gebruikersrisico's in het beleid voor voorwaardelijke toegang van Azure AD kunt u meerdere beleidsregels op basis van gebruikersrisico's maken. Er kunnen verschillende minimale risiconiveaus voor gebruikers zijn vereist voor verschillende gebruikers en apps. Op basis van gebruikersrisico's kunt u beleid maken om toegang te blokkeren, meervoudige verificatie te vereisen, wachtwoord te wijzigen of om te leiden naar Microsoft Cloud App Security om sessiebeleid af te dwingen, zoals aanvullende controle.

De gebruikersrisicovoorwaarde vereist Azure AD Premium P2 omdat azure Identity Protection wordt gebruikt. Dit is een P2-aanbieding. Raadpleeg de documentatie voor voorwaardelijke toegang van Azure AD voor meer informatie over [voorwaardelijke toegang.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO ondersteunt nu apps waarvoor SPNameQualifier moet worden ingesteld wanneer dit wordt aangevraagd

**Type:** Vaste  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Sso
 
Voor sommige SAML-toepassingen moet SPNameQualifier worden geretourneerd in het bevestigingsonderwerp wanneer dit wordt aangevraagd. Azure AD reageert nu correct wanneer een SPNameQualifier wordt aangevraagd in het naam-idbeleid van de aanvraag. Dit werkt ook voor door SP geïnitieerde aanmelding en de door IdP geïnitieerde aanmelding volgt.  Zie Single Sign-On SAML protocol (SAML-protocol voor enkelvoudige Azure Active Directory) voor meer informatie over het [SAML-protocol in Sign-On.](../develop/single-sign-on-saml-protocol.md)

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Collaboration ondersteuning voor het uitnodigen van MSA- en Google-gebruikers in Azure Government tenants

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 

Azure Government tenants die gebruikmaken van de B2B-samenwerkingsfuncties kunnen nu gebruikers uitnodigen die een Microsoft- of Google-account hebben. Als u wilt weten of uw tenant deze mogelijkheden kan gebruiken, volgt u de instructies in Hoe kan ik zien of [B2B-samenwerking](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) beschikbaar is in mijn Azure US Government-tenant?

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Het gebruikersobject in MS Graph v1 bevat nu de eigenschappen externalUserState en externalUserStateChangedDateTime

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 

De eigenschappen externalUserState en externalUserStateChangedDateTime kunnen worden gebruikt om uitgenodigde B2B-gasten te vinden die hun uitnodiging nog niet hebben geaccepteerd, en om automatisering te bouwen, zoals het verwijderen van gebruikers die hun uitnodigingen na een aantal dagen niet hebben geaccepteerd. Deze eigenschappen zijn nu beschikbaar in MS Graph v1. Raadpleeg Gebruikersresourcetype voor hulp bij het gebruik [van deze eigenschappen.](/graph/api/resources/user)
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Verificatiesessies beheren in voorwaardelijke toegang van Azure AD is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection
 
Met de beheermogelijkheden voor verificatiesessies kunt u configureren hoe vaak uw gebruikers aanmeldingsreferenties moeten verstrekken en of ze referenties moeten verstrekken na het sluiten en opnieuw openen van browsers om meer beveiliging en flexibiliteit in uw omgeving te bieden.
 
Daarnaast wordt verificatiesessiebeheer gebruikt om alleen van toepassing te zijn op de First Factor Authentication op apparaten die zijn samengevoegd met Azure AD, hybride Azure AD en apparaten die zijn geregistreerd bij Azure AD. Het beheer van verificatiesessies is nu ook van toepassing op MFA. Zie Configure [authentication session management with Conditional Access (Beheer van verificatiesessies configureren met voorwaardelijke toegang) voor meer informatie.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - juni 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In juni 2020 hebben we de volgende 29 nieuwe toepassingen toegevoegd aan onze App-galerie met ondersteuning voor federatie:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [Segment](../saas-apps/segment-tutorial.md), [Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [Proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [Hub Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [Zero](https://www.teamzero.com/), [InStation](https://instation.invillia.com/), [edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), SmartKargo , [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [Field iD](../saas-apps/field-id-tutorial.md), [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance](../saas-apps/smartkargo-tutorial.md) [Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

U vindt hier ook de documentatie van alle https://aka.ms/AppsTutorial toepassingen. Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te geven: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-connectors voor External Identities-aanmelding via selfservice zijn nu beschikbaar als openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
External Identities API-connectors kunt u gebruikmaken van web-API's voor het integreren van selfservice-aanmelding met externe cloudsystemen. Dit betekent dat u nu web-API's kunt aanroepen als specifieke stappen in een aanmeldingsstroom om aangepaste cloudwerkstromen te activeren. U kunt bijvoorbeeld API-connectors gebruiken voor het volgende:

- Integreren met aangepaste goedkeuringswerkstromen.
- Identiteits proofing uitvoeren
- Gebruikersinvoergegevens valideren
- Gebruikerskenmerken overschrijven
- Aangepaste bedrijfslogica uitvoeren

Zie USE API connectors to customize and extend [self-service sign-up (API-connectors](../external-identities/api-connectors-overview.md)gebruiken om selfservice-aanmelding aan te passen en uit te breiden) of Customize External Identities self-service sign-up with web API integrations (Api-connectors gebruiken om selfservice-aanmelding aan te passen en uit te breiden) of Aangepaste [External Identities-aanmelding met web-API-integraties.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Op aanvraag inrichten en binnen enkele seconden gebruikers in uw apps krijgen

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
De Azure AD-inrichtingsservice werkt momenteel op cyclische basis. De service wordt elke 40 minuten uitgevoerd. Met [de inrichtingsmogelijkheid op aanvraag kunt](https://aka.ms/provisionondemand) u een gebruiker kiezen en deze binnen enkele seconden inrichten. Op deze manier kunt u snel problemen met de inrichting oplossen, zonder dat u opnieuw hoeft op te starten om de inrichtingscyclus opnieuw te starten. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nieuwe machtiging voor het gebruik van Azure AD-rechtenbeheer in Graph

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Rechtenbeheer
 
Er is nu een nieuwe gedelegeerde machtiging EntitlementManagement.Read.All beschikbaar voor gebruik met de rechten-Beheer API in Microsoft Graph bètaversie. Zie Werken met de Azure AD-rechtenbeheer-API voor meer informatie over [de beschikbare API's.](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)

---

### <a name="identity-protection-apis-available-in-v10"></a>Identity Protection-API's die beschikbaar zijn in v1.0

**Type:** Nieuwe functie  
**Servicecategorie:** Identity Protection  
**Productmogelijkheid:** Identity Security & Protection
 
De api's riskyUsers en riskDetections Microsoft Graph zijn nu algemeen beschikbaar. Nu ze beschikbaar zijn op het v1.0-eindpunt, nodigen we u uit om ze in productie te gebruiken. Raadpleeg de Microsoft Graph voor [meer informatie.](/graph/api/resources/identityprotectionroot)
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Gevoeligheidslabels voor het toepassen van beleid op Microsoft 365 groepen is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheid:** Samenwerking
 

U kunt nu gevoeligheidslabels maken en de labelinstellingen gebruiken om beleid toe te passen op Microsoft 365 groepen, waaronder privacybeleid (openbaar of privé) en toegangsbeleid voor externe gebruikers. U kunt een label maken met het privacybeleid privé en beleid voor externe gebruikerstoegang om gastgebruikers niet toe te staan. Wanneer een gebruiker dit label op een groep van toepassing is, is de groep privé en mogen er geen gastgebruikers worden toegevoegd aan de groep. 

Gevoeligheidslabels zijn belangrijk om uw bedrijfskritieke gegevens te beveiligen en u in staat te stellen groepen op schaal, op een compatibele en veilige manier te beheren. Raadpleeg Gevoeligheidslabels toewijzen aan Microsoft 365 groepen in Azure Active Directory (preview) voor hulp bij het gebruik [van gevoeligheidslabels.](../enterprise-users/groups-assign-sensitivity-labels.md)
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Updates voor ondersteuning voor Microsoft Identity Manager voor Azure AD Premium klanten

**Type:** Functie gewijzigd  
**Servicecategorie:** Microsoft Identity Manager  
**Productmogelijkheid:** Identity Lifecycle Management
 
Ondersteuning voor Azure is nu beschikbaar voor Azure AD-integratieonderdelen van Microsoft Identity Manager 2016 tot het einde van uitgebreide ondersteuning voor Microsoft Identity Manager 2016. Lees meer op [Support update for Azure AD Premium customers using Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Het gebruik van groepslidmaatschapsvoorwaarden in de configuratie van SSO-claims is verhoogd

**Type:** Functie gewijzigd  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Sso
 
Voorheen was het aantal groepen dat u kon gebruiken wanneer u claims voorwaardelijk wijzigt op basis van groepslidmaatschap binnen een configuratie van één toepassing beperkt tot 10. Het gebruik van groepslidmaatschapsvoorwaarden in de configuratie van SSO-claims is nu verhoogd tot maximaal 50 groepen. Raadpleeg Configuratie van SSO-claims voor Bedrijfstoepassingen voor meer informatie over het [configureren van claims.](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions) 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Basisopmaak inschakelen voor het onderdeel Tekst van aanmeldingspagina in de huisstijl van het bedrijf.

**Type:** Functie gewijzigd  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
De functionaliteit Huisstijl van het bedrijf op de aanmeldingservaring van Azure AD/Microsoft 365 is bijgewerkt zodat de klant hyperlinks en eenvoudige opmaak kan toevoegen, waaronder vet lettertype, onderstreping en italics. Zie Huisstijl toevoegen aan de Azure Active Directory aanmeldingspagina van uw organisatie voor hulp [bij het gebruik van deze functionaliteit.](./customize-branding.md)

---

### <a name="provisioning-performance-improvements"></a>Prestatieverbeteringen inrichten

**Type:** Functie gewijzigd  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
De inrichtingsservice is bijgewerkt om de tijd voor het voltooien van [een incrementele cyclus](../app-provisioning/how-provisioning-works.md#incremental-cycles) te verminderen. Dit betekent dat gebruikers en groepen sneller in hun toepassingen worden ingericht dan voorheen. Alle nieuwe inrichtingstaken die na 10-6-2020 zijn gemaakt, profiteren automatisch van de prestatieverbeteringen. Toepassingen die zijn geconfigureerd voor inrichting vóór 10-6-2020, moeten na 10-6-2020 eenmaal opnieuw worden opgestart om te profiteren van de prestatieverbeteringen. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Aankondiging van afschaffing van ADAL en MS Graph Parity

**Type:** Afgekeurd  
**Servicecategorie:** N/a  
**Productmogelijkheid:** Levenscyclusbeheer van apparaten

Nu Microsoft Authentication Libraries (MSAL) beschikbaar is, voegen we geen nieuwe functies meer toe aan de Azure Active Directory Authentication Libraries (ADAL) en beëindigen we beveiligingspatches op 30 juni 2022. Raadpleeg Toepassingen migreren naar [Microsoft Authentication Library (MSAL)](../develop/msal-migration.md)voor meer informatie over het migreren naar MSAL.

Daarnaast hebben we het werk voltooid om alle Azure AD Graph-functionaliteit beschikbaar te maken via MS Graph. Azure AD Graph API's ontvangen dus alleen bugfix- en beveiligingsfixes tot en met 30 juni 2022. Zie Uw toepassingen bijwerken voor het gebruik van [Microsoft Authentication Library](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) en Microsoft Graph API voor Microsoft Graph informatie
 
---
## <a name="may-2020"></a>Mei 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Retirement of properties in signIns, riskyUsers, and riskDetections API's

**Type:** Plannen voor wijziging  
**Servicecategorie:** Identiteitsbeveiliging  
**Productmogelijkheid:** Identity Security & Protection

Op dit moment worden geïndesereerde typen gebruikt om de eigenschap riskType in zowel de RISKDetections-API als riskyUserHistoryItem (in preview) weer te geven. Geïnventeerde typen worden ook gebruikt voor de eigenschap riskEventTypes in de signIns-API. In de toekomst worden deze eigenschappen weergegeven als tekenreeksen. 

Klanten moeten voor 9 september 2020 overstappen op de eigenschap riskEventType in de bèta-riskDetections en riskyUserHistoryItem-API en naar riskEventTypes_v2-eigenschap in de bèta-signIns-API. Op die datum worden de huidige eigenschappen riskType en riskEventTypes niet meer gebruikt. Raadpleeg Wijzigingen in eigenschappen van risicogebeurtenissen en [Identity Protection-API's](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)op Microsoft Graph.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Afschaffing van de eigenschap riskEventTypes in signIns v1.0 API op Microsoft Graph

**Type:** Plannen voor wijziging  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Identity Security & Protection

Geïnventereerde typen schakelen over naar tekenreekstypen bij het weergeven van risicogebeurteniseigenschappen in Microsoft Graph september 2020. Deze wijziging is niet alleen van invloed op de preview-API's, maar ook op de in-productie signIns-API.

We hebben een nieuwe eigenschap riskEventsTypes_v2 (tekenreeks) geïntroduceerd in de SIGNIns v1.0 API. We zullen de huidige eigenschap riskEventTypes (enum) op 11 juni 2022 in overeenstemming met ons Microsoft Graph afschaffingsbeleid. Klanten moeten voor 11 juni 2022 riskEventTypes_v2 in de v1.0 signIns-API overstappen naar de eigenschap riskEventTypes_v2 v1.0.0. Raadpleeg de eigenschap [RiskEventTypes in signIns v1.0 API](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)op Microsoft Graph voor meer informatie.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Toekomstige wijzigingen in MFA-e-mailmeldingen

**Type:** Plannen voor wijziging  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 

We brengen de volgende wijzigingen aan in de e-mailmeldingen voor cloud-MFA:

E-mailmeldingen worden verzonden vanaf het volgende adres: azure-noreply@microsoft.com en msonlineservicesteam@microsoftonline.com . We werken de inhoud van e-mailberichten voor fraudewaarschuwingen bij om beter aan te geven welke stappen nodig zijn om gebruik te deblokkeren.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nieuwe selfserviceregistratie voor gebruikers in federatief domeinen die geen toegang hebben tot Microsoft Teams omdat ze niet zijn gesynchroniseerd met Azure Active Directory.

**Type:** Plannen voor wijziging  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 

Momenteel hebben gebruikers die zich in domeinen die zijn ge federeerd in Azure AD, maar die niet zijn gesynchroniseerd met de tenant, geen toegang tot Teams. Vanaf eind juni kunnen ze dit met deze nieuwe functie doen door de bestaande functie voor geverifieerde aanmelding via e-mail uit te breiden. Hierdoor kunnen gebruikers die zich kunnen aanmelden bij een federatief IdP, maar die nog geen gebruikersobject in Azure ID hebben, automatisch een gebruikersobject maken en worden geverifieerd voor Teams. Hun gebruikersobject wordt gemarkeerd als 'selfservice registreren'. Dit is een uitbreiding van de bestaande mogelijkheid om via e-mail geverifieerde zelfregistratie uit te voeren die gebruikers in beheerde domeinen kunnen doen en kunnen worden beheerd met dezelfde vlag. Deze wijziging wordt in de volgende twee maanden uitgerold. Kijk hier voor [documentatie-updates.](../enterprise-users/directory-self-service-signup.md)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Toekomstige oplossing: Het OIDC-detectiedocument voor de Azure Government cloud wordt bijgewerkt om te verwijzen naar de juiste Graph-eindpunten.

**Type:** Plannen voor wijziging  
**Servicecategorie:** Onafhankelijke clouds  
**Productmogelijkheid:** Gebruikersverificatie
 
Vanaf juni worden met het OIDC-detectiedocument Microsoft Identity Platform en [het OpenID Connect-protocol](../develop/v2-protocols-oidc.md) op het [Azure Government-cloud-eindpunt](../develop/authentication-national-cloud.md) (login.microsoftonline.us) het juiste Eindpunt voor nationale [cloudgrafieken](/graph/deployments) ( of ) op basis van de https://graph.microsoft.us opgegeven https://dod-graph.microsoft.us) tenant.  Het bevat momenteel het onjuiste veld Graph.microsoft.com (msgraph_host Graph Endpoint).  

Deze opgeloste fout wordt geleidelijk over ongeveer twee maanden uitgerold.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government kunnen gebruikers zich niet meer aanmelden bij login.microsoftonline.com

**Type:** Plannen voor wijziging  
**Servicecategorie:** Onafhankelijke clouds  
**Productmogelijkheid:** Gebruikersverificatie
 
Op 1 juni 2018 is de officiële Azure Active Directory (Azure AD)-instantie voor Azure Government gewijzigd van https://login-us.microsoftonline.com in https://login.microsoftonline.us . Als u eigenaar bent van een toepassing in Azure Government tenant, moet u uw toepassing bijwerken om gebruikers aan te melden op het .us-eindpunt.

Vanaf 5 mei begint Azure AD met het afdwingen van de eindpuntwijziging, waardoor Azure Government-gebruikers zich niet kunnen aanmelden bij apps die worden gehost in Azure Government-tenants met behulp van het openbare eindpunt (microsoftonline.com). Bij beïnvloede apps wordt de fout AADSTS900439 - USGClientNotSupportedOnPublicEndpoint weergegeven. 

Deze wijziging zal geleidelijk worden implementatie, met afdwinging naar verwachting voltooid voor alle apps van juni 2020. Zie de blogpost Azure Government [meer informatie.](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SamL Single Logout request verzendt nu NameID in de juiste indeling

**Type:** Vaste  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
Wanneer een gebruiker klikt op de uitmelding (bijvoorbeeld in de MyApps-portal), verzendt Azure AD een SAML Single Logout-bericht naar elke app die actief is in de gebruikerssessie en een aanmeldings-URL heeft geconfigureerd. Deze berichten bevatten een NameID in een permanente indeling.

Als het oorspronkelijke SAML-aanmeldingsteken een andere indeling heeft gebruikt voor NameID (bijvoorbeeld e-mail/UPN), kan de SAML-app de NameID in het logout-bericht niet correleren met een bestaande sessie (omdat de NameID's die in beide berichten worden gebruikt verschillend zijn), waardoor het aanmeldingsbericht is verwijderd door de SAML-app en de gebruiker aangemeld blijft. Deze oplossing zorgt ervoor dat het afmeldingsbericht consistent is met de NameID die voor de toepassing is geconfigureerd.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>De rol Van hybride identiteitsbeheerder is nu beschikbaar met Cloud provisioning

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD-cloud inrichten  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
IT-beheerders kunnen de nieuwe rol 'Hybride beheerder' gaan gebruiken als minst bevoorrechte rol voor het instellen van Azure AD Connect Cloud provisioning. Met deze nieuwe rol hoeft u de rol van globale beheerder niet langer te gebruiken om cloud provisioning in te stellen en te configureren. [Meer informatie](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nieuwe federatief apps beschikbaar in azure AD-toepassingsgalerie - mei 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In mei 2020 hebben we de volgende 36 nieuwe toepassingen toegevoegd aan onze App-galerie met ondersteuning voor federatie:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Commerce Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), Smart Global [Governance](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), EasySSO voor [Confluence](../saas-apps/easysso-for-confluence-tutorial.md), EasySSO voor [BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO](../saas-apps/easysso-for-bamboo-tutorial.md)voor Bamboo , [Torii](../saas-apps/torii-tutorial.md), [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [Humanage](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander](../saas-apps/textexpander-tutorial.md), [Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

U vindt hier ook de documentatie van alle https://aka.ms/AppsTutorial toepassingen.

Lees hier de details om uw toepassing in de Azure AD-app-galerie weer te https://aka.ms/AzureADAppRequest geven.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus Alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection

[Met de modus Alleen rapport voor voorwaardelijke toegang van Azure AD kunt](../conditional-access/concept-conditional-access-report-only.md) u het resultaat van een beleid evalueren zonder toegangsbesturingselementen af te afdwingen. U kunt alleen-rapporterende beleidsregels in uw organisatie testen en inzicht krijgen in de impact ervan voordat u ze inschakelen, waardoor implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie gezien van de modus alleen-rapporteren: meer dan 26 miljoen gebruikers vallen al binnen het bereik van een beleid voor alleen-rapporteren. Met de aankondiging van vandaag wordt standaard nieuw beleid voor voorwaardelijke toegang van Azure AD gemaakt in de modus alleen-rapporteren. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die gebruikmaken van de MS Graph-API's, kunt u beleid voor [alleen-rapporteren ook programmatisch](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) beheren. 

---

### <a name="self-service-sign-up-for-guest-users"></a>Selfservice voor aanmelden voor gastgebruikers

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
Met External Identities in Azure AD kunt u personen buiten uw organisatie toegang geven tot uw apps en resources, terwijl ze zich kunnen aanmelden met de identiteit van hun voorkeur. Wanneer u een toepassing deelt met externe gebruikers, weet u mogelijk niet altijd van tevoren wie toegang tot de toepassing nodig heeft. Met [selfservice-aanmelding](../external-identities/self-service-sign-up-overview.md)kunt u gastgebruikers in staat stellen zich te registreren en een gastaccount voor uw LOB-apps (Line-Of-Business) te verkrijgen. De aanmeldingsstroom kan worden gemaakt en aangepast ter ondersteuning van Azure AD en sociale identiteiten. U kunt ook aanvullende informatie over de gebruiker verzamelen tijdens de aanmelding.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap Inzichten en rapportage voor voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection

De [werkmap inzichten en rapportage](../conditional-access/howto-conditional-access-insights-reporting.md) biedt beheerders een overzichtsweergave van voorwaardelijke toegang van Azure AD in hun tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in enkele minuten kunt instellen [met de volgende instructies.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Om het dashboard beter vindbaar te maken, hebben we het verplaatst naar het nieuwe tabblad Inzichten en rapportage in het menu Voorwaardelijke toegang van Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blade Beleidsdetails voor voorwaardelijke toegang is in openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection

Op de blade [met nieuwe beleidsdetails](../conditional-access/troubleshoot-conditional-access.md) worden de toewijzingen, voorwaarden en besturingselementen weergegeven die zijn voldaan tijdens de evaluatie van het beleid voor voorwaardelijke toegang. U kunt de blade openen door een rij te selecteren op de tabbladen Voorwaardelijke toegang of Alleen rapport van de aanmeldingsgegevens.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nieuwe querymogelijkheden voor mapobjecten in Microsoft Graph zijn in openbare preview

**Type:** Nieuwe functie  
**Servicecategorie:** MS **Graph-productmogelijkheid:** Ontwikkelaarservaring

Er worden nieuwe mogelijkheden geïntroduceerd voor Microsoft Graph API's voor mapobjecten, waardoor count-, zoek-, filter- en sorteerbewerkingen worden inschakelen. Dit geeft ontwikkelaars de mogelijkheid om snel query's uit te voeren op onze Directory-objecten zonder tijdelijke oplossingen zoals filteren in het geheugen en sorteren. Meer informatie vindt u in [dit blogbericht](https://aka.ms/CountFilterMSGraphAAD).

We zijn momenteel in openbare preview en zijn op zoek naar feedback. Stuur uw opmerkingen met deze [korte enquête](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Een aanmelding op basis van SAML configureren met behulp van Microsoft Graph API (bèta)

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Sso
 
Ondersteuning voor het maken en configureren van een toepassing vanuit de Azure AD-galerie met behulp van MS Graph API's in Beta is nu beschikbaar. Als u een aanmelding op basis van SAML moet instellen voor meerdere exemplaren van een toepassing, bespaart u tijd door de Microsoft Graph-API's te gebruiken om de configuratie van een aanmelding op basis van [SAML te automatiseren.](/graph/application-saml-sso-configure-api)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - mei 2020

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure-console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML-tokenversleuteling is algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Sso
 
[Met SAML-tokenversleuteling](../manage-apps/howto-saml-token-encryption.md) kunnen toepassingen worden geconfigureerd voor het ontvangen van versleutelde SAML-assertie. De functie is nu algemeen beschikbaar in alle clouds.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Groepsnaamclaims in toepassingstokens zijn algemeen beschikbaar

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Sso
 
De groepsclaims die in een token zijn uitgegeven, kunnen nu worden beperkt tot alleen de groepen die zijn toegewezen aan de toepassing.  Dit is vooral belangrijk wanneer gebruikers lid zijn van een groot aantal groepen en er een risico bestaat dat de tokengroottelimieten worden overschreden. Nu deze nieuwe mogelijkheid is aanwezig, is de mogelijkheid om [groepsnamen toe te voegen aan tokens](../hybrid/how-to-connect-fed-group-claims.md) algemeen beschikbaar.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback ondersteunt nu het instellen van kenmerken van een werktelefoonnummer

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Identity Lifecycle Management
 
We hebben de Workday Writeback-inrichtings-app verbeterd om nu ondersteuning te bieden voor het terugschrijven van kenmerken van werktelefoonnummer en mobiel nummer. Naast e-mail en gebruikersnaam kunt u nu de Workday Writeback-inrichtings-app configureren om telefoonnummerwaarden van Azure AD naar Workday te sturen. Raadpleeg de zelfstudie [Workday Writeback-app](../saas-apps/workday-writeback-tutorial.md) voor meer informatie over het configureren van het terugschrijven van telefoonnummers. 

---

### <a name="publisher-verification-preview"></a>Verificatie van uitgever (preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Ontwikkelaarservaring
 
Verificatie van uitgevers (preview) helpt beheerders en eindgebruikers om inzicht te krijgen in de echtheid van toepassingsontwikkelaars die integreren met het Microsoft-identiteitsplatform. Raadpleeg Verificatie van uitgever [(preview) voor meer informatie.](../develop/publisher-verification-overview.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Autorisatiecodestroom voor apps met één pagina

**Type:** **Functieservicecategorie gewijzigd: Mogelijkheid** van **verificatieproduct:** Ontwikkelaarservaring

Vanwege cookiebeperkingen van derden van de moderne browser, zoals [Safari ITP,](../develop/reference-third-party-cookies-spas.md)moeten SPA's de autorisatiecodestroom gebruiken in plaats van de impliciete stroom om eenmalige aanmelding te onderhouden; MSAL.js v 2.x ondersteunt nu de autorisatiecodestroom. Er als bijbehorende updates voor de Azure Portal zodat u uw spa kunt bijwerken naar type "spa" en de auth code stroom gebruiken. Raadpleeg voor hulp Quickstart: Gebruikers aanmelden en een toegangsteken krijgen in een [JavaScript-spa met behulp van de auth-codestroom](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Verbeterde filtering voor apparaten is beschikbaar als openbare preview

**Type:** Functie gewijzigd   
**Servicecategorie:** Productmogelijkheid voor **apparaatbeheer:** Levenscyclusbeheer van apparaten
 
Voorheen waren 'Ingeschakeld' en 'Activiteitsdatum' de enige filters die u kon gebruiken. U kunt nu uw [lijst met apparaten filteren op](../devices/device-management-azure-portal.md#device-list-filtering-preview)meer eigenschappen, waaronder type besturingssysteem, jointype, naleving en meer. Deze toevoegingen moeten het zoeken naar een bepaald apparaat vereenvoudigen.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>De nieuwe App-registraties voor Azure AD B2C is nu algemeen beschikbaar

**Type:** Functie gewijzigd   
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
De nieuwe App-registraties voor Azure AD B2C is nu algemeen beschikbaar. 

Voorheen moest u uw B2C-consumententoepassingen afzonderlijk van de rest van uw apps beheren met behulp van de verouderde ervaring 'Toepassingen'. Dit betekende verschillende ervaringen voor het maken van apps op verschillende plaatsen in Azure.

De nieuwe ervaring toont alle B2C-app-registraties en Azure AD-app-registraties op één plek en biedt een consistente manier om deze te beheren. Of u nu een klantgerichte app wilt beheren of een app die toegang heeft tot Microsoft Graph om Azure AD B2C-resources programmatisch te beheren, u hoeft maar één manier te leren om dingen te doen.

U kunt de nieuwe ervaring bereiken door te navigeren in Azure AD B2C service en de blade App-registraties selecteren. De ervaring is ook toegankelijk vanuit de Azure Active Directory service.

De Azure AD B2C App-registraties is gebaseerd op de algemene [ervaring voor app-registratie](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) voor Azure AD-tenants, maar is afgestemd op Azure AD B2C. De verouderde ervaring 'Toepassingen' wordt in de toekomst afgeschaft.

Ga voor meer informatie naar [The New app registration experience for Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Gecombineerde ervaring voor registratie van beveiligingsgegevens is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Servicecategorie:** Verificaties (aanmeldingen)

**Productmogelijkheid:** Identity Security & Protection

De gecombineerde registratie-ervaring voor Multi-Factor Authentication (MFA) en Self-Service Wachtwoord opnieuw instellen (SSPR) is nu algemeen beschikbaar. Met deze nieuwe registratie-ervaring kunnen gebruikers zich registreren voor MFA en SSPR in één stapsgewijs proces. Wanneer u de nieuwe ervaring voor uw organisatie implementeert, kunnen gebruikers zich in minder tijd en met minder problemen registreren. Bekijk hier de [blogpost](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Continue toegangsevaluatie

**Type:** Nieuwe functie

**Servicecategorie:** Verificaties (aanmeldingen)

**Productmogelijkheid:** Identity Security & Protection

Continue toegangsevaluatie is een nieuwe beveiligingsfunctie waarmee bijna in realtime beleid kan worden afdwingen op relying parties die Azure AD-toegangstokens gebruiken wanneer er gebeurtenissen plaatsvinden in Azure AD (zoals het verwijderen van gebruikersaccounts). Deze functie wordt eerst uitgerold voor Teams- en Outlook-clients. Lees onze blog en documentatie [voor](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) meer [informatie.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Aanmelden via SMS: Firstline Workers kunnen zich met hun telefoonnummer en zonder wachtwoord aanmelden bij toepassingen met ondersteuning van Azure AD

**Type:** Nieuwe functie

**Servicecategorie:** Verificaties (aanmeldingen)

**Productmogelijkheid:** Gebruikersverificatie

Office start een reeks mobiele bedrijfsapps die geschikt zijn voor niet-traditionele organisaties en voor werknemers in grote organisaties die geen e-mail gebruiken als primaire communicatiemethode. Deze apps zijn gericht op werknemers in de frontline, kantoorloze werknemers, agenten in het veld of winkelmedewerkers die mogelijk geen e-mailadres van hun werkgever krijgen, toegang hebben tot een computer of tot IT. Met dit project kunnen deze werknemers zich aanmelden bij zakelijke toepassingen door een telefoonnummer in te voeren en een code af te ronden. Raadpleeg onze beheerdersdocumentatie [](../authentication/howto-authentication-sms-signin.md) en [eindgebruikersdocumentatie voor meer informatie.](../user-help/sms-sign-in-explainer.md)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Interne gebruikers uitnodigen om B2B-samenwerking te gebruiken

**Type:** Nieuwe functie

**Servicecategorie:** B2b

**Productmogelijkheid:**

We breiden de mogelijkheid voor B2B-uitnodigingen uit zodat bestaande interne accounts in de toekomst kunnen worden uitgenodigd voor het gebruik van B2B-samenwerkingsreferenties. Dit wordt gedaan door het gebruikersobject door te geven aan de Invite-API, naast typische parameters zoals het uitgenodigde e-mailadres. De object-id, UPN, groepslidmaatschap, app-toewijzing, enzovoort van de gebruiker blijven intact, maar in de toekomst gebruiken ze B2B om te verifiëren met de referenties van hun thuisten tenant in plaats van de interne referenties die ze vóór de uitnodiging hebben gebruikt. Zie de documentatie voor [meer informatie.](../external-identities/invite-internal-users.md)

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>De modus Alleen rapport voor voorwaardelijke toegang is nu algemeen beschikbaar

**Type:** Nieuwe functie

**Servicecategorie:** Voorwaardelijke toegang

**Productmogelijkheid:** Identity Security & Protection

[Met de modus Alleen rapport voor voorwaardelijke toegang van Azure AD](../conditional-access/concept-conditional-access-report-only.md) kunt u het resultaat van een beleid evalueren zonder toegangsbesturingselementen af te afdwingen. U kunt alleen-rapporterende beleidsregels in uw organisatie testen en inzicht krijgen in de impact ervan voordat u ze inschakelen, waardoor implementatie veiliger en eenvoudiger wordt. In de afgelopen maanden hebben we een sterke acceptatie gezien van de modus alleen-rapporteren, met meer dan 26 miljoen gebruikers die al binnen het bereik van een alleen-rapportbeleid vallen. Met deze aankondiging wordt standaard nieuw beleid voor voorwaardelijke toegang van Azure AD gemaakt in de modus alleen-rapporteren. Dit betekent dat u de impact van uw beleid kunt bewaken vanaf het moment dat ze worden gemaakt. En voor degenen die gebruikmaken van de MS Graph API's, kunt u ook programmatisch beleid voor [alleen-rapporteren beheren.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>De werkmap voor inzichten en rapportage voor voorwaardelijke toegang is algemeen beschikbaar

**Type:** Nieuwe functie

**Servicecategorie:** Voorwaardelijke toegang

**Productmogelijkheid:** Identity Security & Protection

De werkmap Inzichten [en rapportage voor](../conditional-access/howto-conditional-access-insights-reporting.md) voorwaardelijke toegang biedt beheerders een overzichtsweergave van voorwaardelijke toegang van Azure AD in hun tenant. Met de mogelijkheid om een afzonderlijk beleid te selecteren, kunnen beheerders beter begrijpen wat elk beleid doet en eventuele wijzigingen in realtime controleren. De werkmap streamt gegevens die zijn opgeslagen in Azure Monitor, die u in een paar minuten kunt instellen met [de volgende instructies.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Om het dashboard beter vindbaar te maken, hebben we het verplaatst naar het nieuwe tabblad Inzichten en rapportage in het menu Voorwaardelijke toegang van Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Blade Beleidsdetails voor voorwaardelijke toegang is in openbare preview

**Type:** Nieuwe functie

**Servicecategorie:** Voorwaardelijke toegang

**Productmogelijkheid:** Identity Security & Protection

Op de blade [met nieuwe beleidsdetails](../conditional-access/troubleshoot-conditional-access.md) wordt weergegeven aan welke toewijzingen, voorwaarden en besturingselementen is voldaan tijdens de evaluatie van het beleid voor voorwaardelijke toegang. U kunt de blade openen door een rij te selecteren op **de** tabbladen Voorwaardelijke toegang of Alleen **rapport** van de aanmeldingsgegevens.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - april 2020

**Type:** Nieuwe functie

**Servicecategorie:** Enterprise Apps

**Productmogelijkheid:** Integratie van derden

In april 2020 hebben we deze 31 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie: 

[SincroPool-apps,](https://www.sincropool.com/) [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365,](https://lms.365.systems/) [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO voor Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft 365 [Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), [Honestly](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md) [,Flow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Harmony](../saas-apps/harmony-tutorial.md), [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md) [,](https://www.made-in-office.com/en/)empower , [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [NITRO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), Nitro [Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md), Trend Micro Web [Security(TMWS)](../saas-apps/trend-micro-tutorial.md)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph voor deltaquery's voor oAuth2PermissionGrant beschikbaar voor openbare preview

**Type:** Nieuwe functie

**Servicecategorie:** MS Graph

**Productmogelijkheid:** Ontwikkelaarservaring

Delta-query voor oAuth2PermissionGrant is beschikbaar voor openbare preview. U kunt nu wijzigingen bijhouden zonder dat u continu poll-Microsoft Graph. [Meer informatie.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph ondersteuning voor deltaquery's voor contactpersoon van de organisatie algemeen beschikbaar

**Type:** Nieuwe functie

**Servicecategorie:** MS Graph

**Productmogelijkheid:** Ontwikkelaarservaring

Delta-query voor organisatiecontacten is algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder continu poll-Microsoft Graph. Vervang alle bestaande code die continu org peiltContactgegevens per deltaquery om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph voor deltaquery's voor toepassingen die algemeen beschikbaar zijn

**Type:** Nieuwe functie

**Servicecategorie:** MS Graph

**Productmogelijkheid:** Ontwikkelaarservaring

Delta-query voor toepassingen is algemeen beschikbaar. U kunt nu wijzigingen in productie-apps bijhouden zonder dat u continu polling-Microsoft Graph. Vervang alle bestaande code die continu toepassingsgegevens peilt door deltaquery's om de prestaties aanzienlijk te verbeteren. [Meer informatie.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph voor deltaquery's voor beheereenheden beschikbaar voor openbare preview

**Type:** Nieuwe functie

**Servicecategorie:** MS Graph

**Productmogelijkheid:** Delta-query voor ontwikkelaarservaring voor beheereenheden is beschikbaar voor openbare preview. U kunt nu wijzigingen bijhouden zonder dat u continu polling-Microsoft Graph. [Meer informatie.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Telefoonnummers voor verificatie en meer beheren in nieuwe Microsoft Graph bèta-API's

**Type:** Nieuwe functie

**Servicecategorie:** MS Graph

**Productmogelijkheid:** Ontwikkelaarservaring

Deze API's zijn een belangrijk hulpmiddel voor het beheren van de verificatiemethoden van uw gebruikers. U kunt nu programmatisch vooraf registreren en de ver authenticators beheren die worden gebruikt voor MFA en selfservice voor wachtwoord opnieuw instellen (SSPR). Dit is een van de meest aangevraagde functies in de Azure AD MFA, SSPR en Microsoft Graph spaties. De nieuwe API's die we in deze golf hebben uitgebracht, bieden u de volgende mogelijkheden:

- Verificatietelefoons van een gebruiker lezen, toevoegen, bijwerken en verwijderen
- Het wachtwoord van een gebruiker opnieuw instellen
- Sms-aanmelding in- en uitschakelen

Zie Azure AD authentication [methods API overview (Api-overzicht van Azure AD-verificatiemethoden) voor meer informatie.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

---

### <a name="administrative-units-public-preview"></a>Openbare preview van beheereenheden

**Type:** Nieuwe functie

**Servicecategorie:** Azure AD-rollen

**Productmogelijkheid:** Access Control

Met beheereenheden kunt u beheerdersmachtigingen verlenen die zijn beperkt tot een afdeling, een regio of een ander door u gedefinieerd segment van uw organisatie. U kunt beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om een beleid op een gedetailleerd niveau in te stellen. Beheerders van gebruikersaccounts kunnen bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor uitsluitend gebruikers die in hun beheereenheid zijn opgenomen.

Met behulp van beheereenheden kan een centrale beheerder het volgende doen:

- Een beheereenheid maken voor gedecentraliseerd beheer van resources
- Een rol met beheerdersmachtigingen alleen toewijzen aan Azure AD-gebruikers in een beheereenheid
- Vul de beheereenheden naar behoefte in met gebruikers en groepen

Zie Beheer van beheereenheden in Azure Active Directory [(preview) voor meer informatie.](../roles/administrative-units.md)

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ingebouwde rollen Printerbeheerder en Printertechnicus

**Type:** Nieuwe functie

**Servicecategorie:** Azure AD-rollen

**Productmogelijkheid:** Access Control

**Printerbeheerder:** gebruikers met deze rol kunnen printers registreren en alle aspecten van alle printerconfiguraties in de Microsoft Universeel afdrukken-oplossing beheren, met inbegrip van de instellingen Universeel afdrukken Connector. Ze kunnen toestemming geven voor alle gedelegeerde aanvragen voor afdrukmachtigingen. Printerbeheerders hebben ook toegang tot afdrukrapporten. 

**Printertechnicus:** gebruikers met deze rol kunnen printers registreren en de printerstatus beheren in de Microsoft Universeel afdrukken oplossing. Ze kunnen ook alle connectorgegevens lezen. Belangrijke taken die een printertechnicus niet kan uitvoeren, zijn het instellen van gebruikersmachtigingen voor printers en het delen van printers. [Meer informatie.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ingebouwde rol van hybride identiteitsbeheerder

**Type:** Nieuwe functie

**Servicecategorie:** Azure AD-rollen

**Productmogelijkheid:** Access Control

Gebruikers met deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in Azure AD inschakelen, configureren en beheren. Met deze rol kunt u Azure AD configureren voor een van de drie ondersteunde verificatiemethoden&#8212;Wachtwoordhashsynchronisatie (PHS), pass-through-verificatie (PTA) of federatie (AD FS of federatieprovider van derden)&#8212;en gerelateerde on-premises infrastructuur implementeren om deze in te schakelen. De on-premises infrastructuur omvat inrichtings- en PTA-agents. Deze rol biedt de mogelijkheid naadloze eenmalige Sign-On aanmelding (S-SSO) in te schakelen voor naadloze verificatie op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Daarnaast biedt deze rol de mogelijkheid om aanmeldingslogboeken te bekijken en toegang te krijgen tot status en analyses voor bewakings- en probleemoplossingsdoeleinden. [Meer informatie.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ingebouwde rol netwerkbeheerder

**Type:** Nieuwe functie

**Servicecategorie:** Azure AD-rollen

**Productmogelijkheid:** Access Control

Gebruikers met deze rol kunnen aanbevelingen voor de netwerkperimeterarchitectuur van Microsoft bekijken die zijn gebaseerd op netwerk-telemetrie van hun gebruikerslocaties. Netwerkprestaties voor Microsoft 365 zijn afhankelijk van een zorgvuldige bedrijfsnetwerkperimeterarchitectuur, die doorgaans specifiek is voor de locatie van gebruikers. Met deze rol kunt u de gevonden gebruikerslocaties en de configuratie van netwerkparameters voor deze locaties bewerken om verbeterde telemetriemetingen en ontwerpaanbevelingen mogelijk te maken. [Meer informatie.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Bulkactiviteit en downloads in de Azure AD-beheerportal

**Type:** Nieuwe functie

**Servicecategorie:** Gebruikersbeheer

**Productmogelijkheid:** Directory

U kunt nu bulkactiviteiten uitvoeren op gebruikers en groepen in Azure AD door een CSV-bestand te uploaden in de Azure AD-beheerportal. U kunt gebruikers maken, gebruikers verwijderen en gastgebruikers uitnodigen. En u kunt leden toevoegen aan en verwijderen uit een groep.

U kunt ook lijsten met Azure AD-resources downloaden via de Azure AD-beheerportal. U kunt de lijst met gebruikers downloaden in de directory, de lijst met groepen in de map en de leden van een bepaalde groep.

Bekijk het volgende voor meer informatie:

- [Gebruikers maken of](../enterprise-users/users-bulk-add.md) [gastgebruikers uitnodigen](../external-identities/tutorial-bulk-invite.md)
- [Gebruikers verwijderen](../enterprise-users/users-bulk-delete.md) of [verwijderde gebruikers herstellen](../enterprise-users/users-bulk-restore.md)
- [Lijst met gebruikers downloaden of](../enterprise-users/users-bulk-download.md) Lijst met groepen [downloaden](../enterprise-users/groups-bulk-download.md)
- [Leden toevoegen (importeren)](../enterprise-users/groups-bulk-import-members.md) [of leden verwijderen](../enterprise-users/groups-bulk-remove-members.md) of lijst met leden [voor](../enterprise-users/groups-bulk-download-members.md) een groep downloaden

---

### <a name="my-staff-delegated-user-management"></a>Mijn medewerkers gedelegeerd gebruikersbeheer

**Type:** Nieuwe functie

**Servicecategorie:** Gebruikersbeheer

**Productmogelijkheid:**

Mijn medewerkers eerstelijnsmanagers, zoals een winkelmanager, kunnen ervoor zorgen dat hun medewerkers toegang hebben tot hun Azure AD-accounts. In plaats van te vertrouwen op een centrale helpdesk, kunnen organisaties algemene taken, zoals het opnieuw instellen van wachtwoorden of het wijzigen van telefoonnummers, delegeren aan een Firstline Manager. Met Mijn medewerkers kunnen gebruikers die geen toegang hebben tot hun account, in slechts een paar klikken weer toegang krijgen, zonder dat er een helpdesk of IT-personeel nodig is. Zie Manage your [users with Mijn medewerkers (preview) (Uw](../roles/my-staff-configure.md) gebruikers beheren met Mijn medewerkers (preview) en Gebruikersbeheer delegeren met [Mijn medewerkers (preview) voor meer informatie.](../user-help/my-staff-team-manager.md)

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Een bijgewerkte ervaring voor eindgebruikers in toegangsbeoordelingen

**Type:** Functie gewijzigd

**Servicecategorie:** Toegangsbeoordelingen

**Productmogelijkheid:** Identiteitsbeheer

We hebben de ervaring van de revisor voor Azure AD-toegangsbeoordelingen bijgewerkt in Mijn apps portal. Eind april zien uw revisoren die zijn aangemeld bij de revisorervaring van Azure AD-toegangsbeoordelingen een banner waarmee ze de bijgewerkte ervaring in de Mijn toegang. Houd er rekening mee dat de bijgewerkte ervaring toegangsbeoordelingen dezelfde functionaliteit biedt als de huidige ervaring, maar met een verbeterde gebruikersinterface naast nieuwe mogelijkheden waarmee uw gebruikers productief kunnen zijn. [Meer informatie over de bijgewerkte ervaring vindt u hier.](../governance/perform-access-review.md) Deze openbare preview duurt tot eind juli 2020. Aan het einde van juli worden revisoren die zich niet hebben gekozen voor de preview-ervaring, automatisch omgeleid Mijn toegang toegangsbeoordelingen uit te voeren. Als u uw revisoren nu permanent wilt over laten overgeschakeld naar de preview-ervaring in Mijn toegang, kunt u hier [een aanvraag indienen.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Apps voor het inrichten en terugschrijven van binnenkomende gebruikers van Workday ondersteunen nu de nieuwste versies van de Workday Web Services-API

**Type:** Functie gewijzigd

**Servicecategorie:** App-inrichting

**Productmogelijkheid:** 

Op basis van feedback van klanten hebben we nu de apps voor het inrichten en terugschrijven van binnenkomende gebruikers van Workday in de galerie met bedrijfsapps bijgewerkt om de nieuwste versies van de WWS-API (Workday Web Services) te ondersteunen. Met deze wijziging kunnen klanten de WWS API-versie opgeven die ze willen gebruiken in de connection string. Hierdoor kunnen klanten meer HR-kenmerken ophalen die beschikbaar zijn in de releases van Workday. De Workday Writeback-app gebruikt nu de aanbevolen Change_Work_Contact_Info Workday-webservice om de beperkingen van de Maintain_Contact_Info.

Als er geen versie is opgegeven in de connection string, blijven de inkomende inrichtingsapps van Workday WWS v21.1 gebruiken Om over te schakelen naar de meest recente Workday-API's voor het inrichten van binnenkomende gebruikers, moeten klanten de connection string bijwerken zoals beschreven [in](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) de zelfstudie en ook de XPATHs bijwerken die worden gebruikt voor Workday-kenmerken zoals beschreven in de [Workday-referentiehandleiding](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)voor kenmerken. 

Als u de nieuwe API wilt gebruiken voor terugschrijven, zijn er geen wijzigingen vereist in de Workday Writeback-inrichtings-app. Zorg er aan de zijde van Workday voor dat het isu-account (Workday Integration System User) machtigingen heeft om het Change_Work_Contact-bedrijfsproces aan te roepen, zoals beschreven in de sectie Zelfstudie Beveiligingsbeleid voor bedrijfsproces [configureren.](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) 

We hebben onze [zelfstudiehandleiding bijgewerkt om](../saas-apps/workday-inbound-tutorial.md) de ondersteuning van de nieuwe API-versie weer te geven.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gebruikers met de standaardtoegangsrol vallen nu binnen het bereik voor inrichting

**Type:** Functie gewijzigd

**Servicecategorie:** App-inrichting

**Productmogelijkheid:** Levenscyclusbeheer voor identiteit

In het verleden waren gebruikers met de standaardtoegangsrol buiten het bereik voor inrichting. We hebben feedback gehoord dat klanten willen dat gebruikers met deze rol binnen het bereik van de inrichting vallen. Vanaf 16 april 2020 kunnen alle nieuwe inrichtingsconfiguraties gebruikers met de standaardtoegangsrol inrichten. Geleidelijk wijzigen we het gedrag voor bestaande inrichtingsconfiguraties om het inrichten van gebruikers met deze rol te ondersteunen. [Meer informatie.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Bijgewerkte gebruikersinterface voor inrichting

**Type:** Functie gewijzigd

**Servicecategorie:** App-inrichting

**Productmogelijkheid:** Levenscyclusbeheer voor identiteit

We hebben onze inrichtingservaring vernieuwd om een meer gerichte beheerweergave te maken. Wanneer u naar de inrichtingsblade voor een bedrijfstoepassing navigeert die al is geconfigureerd, kunt u eenvoudig de voortgang van het inrichten en beheren van acties controleren, zoals het starten, stoppen en opnieuw starten van de inrichting. [Meer informatie.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validatie van dynamische groepregels is nu beschikbaar voor openbare preview

**Type:** Functie gewijzigd

**Servicecategorie:** Groepsbeheer

**Productmogelijkheid:** Samenwerking

Azure Active Directory (Azure AD) biedt nu de middelen om dynamische groepsregels te valideren. Op het **tabblad Regels valideren** kunt u de dynamische regel valideren op basis van de leden van de voorbeeldgroep om te controleren of de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groepsregels willen beheerders weten of een gebruiker of een apparaat lid wordt van de groep. Dit helpt te evalueren of een gebruiker of apparaat voldoet aan de regelcriteria en helpt bij het oplossen van problemen wanneer het lidmaatschap niet wordt verwacht.

Zie Een regel voor dynamisch [groepslidmaatschap valideren (preview) voor meer informatie.](../enterprise-users/groups-dynamic-rule-validation.md)

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Id-beveiligingsscore: beveiligingsinstellingen en MFA-verbeteringsactie-updates

**Type:** Functie gewijzigd

**Servicecategorie:** N/a

**Productmogelijkheid:** Identity Security & Protection

**Ondersteuning van standaardinstellingen voor beveiliging voor Azure AD-verbeteringsacties:** Microsoft Secure Score werkt verbeteringsacties bij ter ondersteuning van standaardinstellingen voor beveiliging [in Azure AD,](./concept-fundamentals-security-defaults.md)waardoor het eenvoudiger wordt om uw organisatie te beschermen met vooraf geconfigureerde beveiligingsinstellingen voor veelvoorkomende aanvallen. Dit is van invloed op de volgende verbeteringsacties:

- Zorg ervoor dat alle gebruikers meervoudige verificatie kunnen voltooien voor beveiligde toegang
- MFA vereisen voor beheerdersrollen
- Beleid inschakelen om verouderde verificatie te blokkeren
 
**Updates van de actie MFA-verbetering:** Microsoft Secure Score heeft drie verbeteringsacties verwijderd die zijn gericht op meervoudige verificatie en er zijn er twee toegevoegd om de noodzaak voor bedrijven om de meest veilige beveiliging te garanderen tijdens het toepassen van beleidsregels die met hun bedrijf werken.

Acties voor verbetering verwijderd:

- Alle gebruikers registreren voor meervoudige verificatie
- MFA vereisen voor alle gebruikers
- MFA vereisen voor azure AD-bevoorrechte rollen

Er zijn verbeteringsacties toegevoegd:

- Zorg ervoor dat alle gebruikers meervoudige verificatie kunnen voltooien voor veilige toegang
- MFA vereisen voor beheerdersrollen

Deze nieuwe verbeteringsacties vereisen dat uw gebruikers of beheerders worden geregistreerd voor meervoudige verificatie (MFA) in uw directory en dat de juiste set beleidsregels wordt ingesteld die voldoen aan de behoeften van uw organisatie. Het belangrijkste doel is flexibiliteit te hebben en ervoor te zorgen dat al uw gebruikers en beheerders zich kunnen verifiëren met meerdere factoren of vragen om identiteitverificatie op basis van risico's. Dit kan de vorm aannemen van meerdere beleidsregels die beslissingen binnen het bereik toepassen of beveiligingsinstellingen instellen (vanaf 16 maart) die Microsoft in staat stellen om gebruikers uit te dagen voor MFA. [Lees meer over wat er nieuw is in Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Maart 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Niet-Azure Active Directory accounts in B2B-update voor maart 2021

**Type:** Plannen voor wijziging  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
**Vanaf 31 maart 2021** biedt Microsoft geen ondersteuning meer voor het inwisselen van uitnodigingen door het maken van niet-Azure Active Directory-accounts (Azure AD) en tenants voor B2B-samenwerkingsscenario's. Ter voorbereiding hiervoor raden we u aan om in te schakelen voor een een [time-e-mail met wachtwoordcodeverificatie.](../external-identities/one-time-passcode.md)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gebruikers met de standaardtoegangsrol vallen binnen het bereik voor inrichting

**Type:** Plannen voor wijziging  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
In het verleden waren gebruikers met de standaardtoegangsrol buiten het bereik voor inrichting. We hebben feedback gehoord dat klanten willen dat gebruikers met deze rol binnen het bereik van inrichting vallen. Er wordt gewerkt aan het implementeren van een wijziging, zodat alle nieuwe inrichtingsconfiguraties gebruikers met de standaardtoegangsrol kunnen inrichten. Geleidelijk wijzigen we het gedrag voor bestaande inrichtingsconfiguraties om het inrichten van gebruikers met deze rol te ondersteunen. Er is geen actie van de klant vereist. Zodra deze wijziging is toegevoegd, wordt [er](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) een update aan onze documentatie toegevoegd.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samenwerking is beschikbaar in Microsoft Azure beheerd door 21Vianet-tenants (Azure China 21Vianet)

**Type:** Plannen voor wijziging  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
De samenwerkingsmogelijkheden van Azure AD B2B worden beschikbaar gesteld in Microsoft Azure beheerd door 21Vianet-tenants (Azure China 21Vianet), zodat gebruikers in een Azure China 21Vianet-tenant naadloos kunnen samenwerken met gebruikers in andere Azure China 21Vianet-tenants. [Meer informatie over Azure AD B2B-samenwerking](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration opnieuw ontwerpen van uitnodigingsmail

**Type:** Plannen voor wijziging  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
De [](../external-identities/invitation-email-elements.md) e-mailberichten die worden verzonden door de Azure AD B2B-samenwerkingsuitnodigingsservice om gebruikers uit te nodigen voor de directory, worden opnieuw ontworpen om de uitnodigingsinformatie en de volgende stappen duidelijker te maken.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Wijzigingen in het HomeRealmDiscovery-beleid worden weergegeven in de auditlogboeken

**Type:** Vaste  
**Servicecategorie:** Audit  
**Productmogelijkheid:** Controle & rapportage
 
Er is een fout opgelost waarbij wijzigingen in [het beleid HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) niet zijn opgenomen in de auditlogboeken. U kunt nu zien wanneer en hoe het beleid is gewijzigd en door wie. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - maart 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In maart 2020 hebben we deze 51 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie: 

[Cisco AnyConnect,](../saas-apps/cisco-anyconnect.md) [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md), [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), Wisdom [by Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), Digital [Signage](https://spark-dev.pixelnebula.com/login) [, Logz.io - Waarneembaarheid](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md)van de cloud voor technici , [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact,](https://bizzcontact.app/) [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare,](http://www.signshare.com/) [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision Compas,](../saas-apps/netvision-compas-tutorial.md) [FCM HUB,](../saas-apps/fcm-hub-tutorial.md) [WANT A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](../saas-apps/golinks-tutorial.md) [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [LIFT](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](../saas-apps/skills-workflow-tutorial.md), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), IP [Platform](../saas-apps/ip-platform-tutorial.md), [InVision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio voor Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md), [Campus Café](../saas-apps/campus-cafe-tutorial.md), [Catchpoint](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration beschikbaar in Azure Government tenants

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** B2B/B2C
 
De samenwerkingsfuncties van Azure AD B2B zijn nu beschikbaar tussen Azure Government tenants.  Als u wilt weten of uw tenant deze mogelijkheden kan gebruiken, volgt u de instructies in Hoe kan ik zien of [B2B-samenwerking](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)beschikbaar is in mijn Azure US Government-tenant? .

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor-integratie voor Azure-logboeken is nu beschikbaar in Azure Government

**Type:** Nieuwe functie  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle & rapportage
 
Azure Monitor integratie met Azure AD-logboeken is nu beschikbaar in Azure Government. U kunt Azure AD-logboeken (audit- en aanmeldingslogboeken) doorversteken naar een opslagaccount, Event Hub en Log Analytics. Raadpleeg de gedetailleerde documentatie [en implementatieplannen](../reports-monitoring/concept-activity-logs-azure-monitor.md) voor [rapportage en bewaking voor](../reports-monitoring/plan-monitoring-and-reporting.md) Azure AD-scenario's.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identiteitsbeveiliging vernieuwen in Azure Government

**Type:** Nieuwe functie  
**Servicecategorie:** Identiteitsbeveiliging  
**Productmogelijkheid:** Identity Security & Protection

We zijn blij te kunnen delen dat we [](../identity-protection/overview-identity-protection.md)nu de vernieuwde Azure AD Identity Protection   hebben uitgerold in [Microsoft Azure Government portal.](https://portal.azure.us/) Zie onze aankondigingsblogpost [voor meer informatie.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Herstel na noodherstel: uw inrichtingsconfiguratie downloaden en opslaan

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Levenscyclusbeheer voor identiteit
 
De Azure AD-inrichtingsservice biedt een uitgebreide set configuratiemogelijkheden. Klanten moeten hun configuratie kunnen opslaan zodat ze er later naar kunnen verwijzen of kunnen terugdraaien naar een bekende goede versie. We hebben de mogelijkheid toegevoegd om uw inrichtingsconfiguratie te downloaden als een JSON-bestand en deze te uploaden wanneer u het nodig hebt. [Meer informatie](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self-service voor wachtwoord opnieuw instellen) vereist nu twee poorten voor beheerders in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet) 

**Type:** Functie gewijzigd  
**Servicecategorie: Self-Service** wachtwoord opnieuw instellen  
**Productmogelijkheid:** Identity Security & Protection
 
Voorheen in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet), hadden beheerders die selfservice voor wachtwoord opnieuw instellen (SSPR) gebruiken om hun eigen wachtwoorden opnieuw in te stellen slechts één poort (uitdaging) nodig om hun identiteit te bewijzen. In openbare en andere nationale clouds moeten beheerders over het algemeen twee poorten gebruiken om hun identiteit te bewijzen bij het gebruik van SSPR. Maar omdat we geen ondersteuning voor sms- of telefoongesprekken in Azure China 21Vianet, hebben beheerders het opnieuw instellen van een wachtwoord met één poort toegestaan.

We maken SSPR-functiepariteit tussen Azure China 21Vianet en de openbare cloud. In de toekomst moeten beheerders twee poorten gebruiken bij het gebruik van SSPR. Meldingen en codes voor sms-berichten, telefoongesprekken en Authenticator-apps worden ondersteund. [Meer informatie](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>De wachtwoordlengte is beperkt tot 256 tekens

**Type:** Functie gewijzigd  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
Om de betrouwbaarheid van de Azure AD-service te garanderen, zijn gebruikerswachtwoorden nu beperkt tot 256 tekens. Gebruikers met een wachtwoord dat langer is dan dit wordt gevraagd om hun wachtwoord te wijzigen bij volgende aanmeldingen, door contact op te nemen met hun beheerder of door de selfservicefunctie voor wachtwoord opnieuw instellen te gebruiken.

Deze wijziging is ingeschakeld op 13 maart 2020 om 10:00 PST (18:00 UTC) en de fout is AADSTS 50052, InvalidPasswordExceedsMaxLength. Zie de [kennisgeving over de wijziging die zich voor](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) het breken voordeed voor meer informatie.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD-aanmeldingslogboeken zijn nu beschikbaar voor alle gratis tenants via de Azure Portal

**Type:** Functie gewijzigd  
**Servicecategorie:** Rapportage  
**Productmogelijkheid:** Controle & rapportage
 
Vanaf nu hebben klanten met gratis tenants maximaal 7 dagen toegang tot de aanmeldingslogboeken van [Azure AD Azure Portal](../reports-monitoring/concept-sign-ins.md) de azure ad-aanmeldingslogboeken. Voorheen waren aanmeldingslogboeken alleen beschikbaar voor klanten Azure Active Directory Premium licenties. Door deze wijziging hebben alle tenants via de portal toegang tot deze logboeken.

> [!NOTE]
> Klanten hebben nog steeds een Premium-licentie (Azure Active Directory Premium P1 of P2) nodig om toegang te krijgen tot de aanmeldingslogboeken via Microsoft Graph API en Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>De optie Voor directorybrede groepen wordt afgeschreven uit Algemene instellingen voor groepen op Azure Portal

**Type:** Afgekeurd  
**Servicecategorie:** Groepsbeheer  
**Productmogelijkheid:** Samenwerking

Om klanten een flexibelere manier te bieden om adreslijstbrede groepen te  maken die het beste voldoen aan hun behoeften, hebben we de optie Groepen voor de hele map van de algemene instellingen in de Azure Portal vervangen door een koppeling naar dynamische groepsdocumentatie  >   . [](../enterprise-users/groups-dynamic-membership.md) We hebben onze documentatie verbeterd met meer instructies, zodat beheerders groepen voor alle gebruikers kunnen maken die gastgebruikers bevatten of uitsluiten.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Toekomstige wijzigingen in aangepaste besturingselementen

**Type:** Plannen voor wijziging  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 
We zijn van plan om de huidige preview-versie van aangepaste besturingselementen te vervangen door een benadering waarmee door de partner geleverde verificatiemogelijkheden naadloos kunnen worden gebruikt met de Azure Active Directory-beheerder en eindgebruikerservaring. Op dit moment gelden voor MFA-oplossingen van partners de volgende beperkingen: ze werken alleen nadat een wachtwoord is ingevoerd; ze fungeren niet als MFA voor stapsgewijse verificatie in andere belangrijke scenario's; en ze kunnen niet worden geïntegreerd met beheerfuncties voor eindgebruikers of beheerdersreferenties. Met de nieuwe implementatie kunnen door partners geleverde verificatiefactoren samenwerken met ingebouwde factoren voor belangrijke scenario's, zoals registratie, gebruik, MFA-claims, stapsgewijs verificatie, rapportage en logboekregistratie. 

Aangepaste besturingselementen worden nog steeds ondersteund in de preview-versie naast het nieuwe ontwerp totdat het algemeen beschikbaar is. Op dat moment geven we klanten de tijd om te migreren naar het nieuwe ontwerp. Vanwege de beperkingen van de huidige benadering gaan we geen nieuwe providers onboarden totdat het nieuwe ontwerp beschikbaar is. We werken nauw samen met klanten en providers en zullen de tijdlijn communiceren naarmate we dichter bij elkaar komen. [Meer informatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Id-beveiligingsscore : updates voor MFA-verbeteringsactie

**Type:** Plannen voor wijziging  
**Servicecategorie:** Mfa  
**Productmogelijkheid:** Identity Security & Protection
 
Microsoft Secure Score verwijdert drie verbeteringsacties die zijn gericht op meervoudige verificatie (MFA) en voegt er twee toe om de noodzaak voor bedrijven om de meest veilige beveiliging te garanderen tijdens het toepassen van beleidsregels die met hun bedrijf werken.

De volgende verbeteringsacties worden verwijderd:

- Alle gebruikers registreren voor MFA
- MFA vereisen voor alle gebruikers
- MFA vereisen voor azure AD-bevoorrechte rollen

De volgende verbeteringsacties worden toegevoegd:

- Zorg ervoor dat alle gebruikers MFA kunnen voltooien voor beveiligde toegang
- MFA vereisen voor beheerdersrollen

Voor deze nieuwe verbeteringsacties moeten uw gebruikers of beheerders voor MFA in uw directory worden geregistreerd en moet de juiste set beleidsregels worden ingesteld die aansluiten op de behoeften van uw organisatie. Het belangrijkste doel is flexibiliteit te hebben en ervoor te zorgen dat al uw gebruikers en beheerders zich kunnen verifiëren met meerdere factoren of vragen om identiteitverificatie op basis van risico's. Dit kan de vorm aannemen van het instellen van standaardinstellingen voor beveiliging, zodat Microsoft kan bepalen wanneer gebruikers MFA moeten aanvragen of meerdere beleidsregels hebben die beslissingen met een bereik toepassen. Als onderdeel van deze updates voor de verbeteringsactie wordt het beveiligingsbeleid voor de basislijn niet meer opgenomen in scoreberekeningen. [Lees meer over wat er in Microsoft Secure Score wordt gebruikt.](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming)

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU selecteren

**Type:** Nieuwe functie  
**Servicecategorie:** Azure AD Domain Services  
**Productmogelijkheid:** Azure AD Domain Services
 
We hebben feedback gehoord dat Azure AD Domain Services meer flexibiliteit willen bij het selecteren van prestatieniveaus voor hun instanties. Vanaf 1 februari 2020 zijn we overgeschakeld van een dynamisch model (waarbij Azure AD de prestaties en prijscategorie bepaalt op basis van het aantal objecten) naar een zelfselectiemodel. Klanten kunnen nu een prestatielaag kiezen die overeenkomt met hun omgeving. Door deze wijziging kunnen we ook nieuwe scenario's inschakelen, zoals resource forests en Premium-functies, zoals dagelijkse back-ups. Het aantal objecten is nu onbeperkt voor alle SKU's, maar we blijven suggesties voor het aantal objecten bieden voor elke laag.

**Er is geen onmiddellijke actie van de klant vereist.** Voor bestaande klanten bepaalt de dynamische laag die op 1 februari 2020 in gebruik was, de nieuwe standaardlaag. Deze wijziging heeft geen invloed op de prijzen of prestaties. In de toekomst moeten Azure AD DS prestatievereisten evalueren naarmate hun directorygrootte en workloadkenmerken veranderen. Schakelen tussen servicelagen blijft een bewerking zonder downtime en we verplaatsen klanten niet meer automatisch naar nieuwe lagen op basis van de groei van hun directory. Bovendien zijn er geen prijsverhogingen en worden nieuwe prijzen afgestemd op ons huidige factureringsmodel. Zie de documentatie over Azure AD DS [SKU's](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) en Azure AD Domain Services pagina [met prijzen voor meer informatie.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - februari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
In februari 2020 hebben we deze 31 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie: 

[IamIP Patent Platform](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [NS1 SSO voor Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In Case of Crisis - Online [Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), BIC Cloud [Design](../saas-apps/bic-cloud-design-tutorial.md), [Beekeeper Azure AD Data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), New Relic [(beperkte release)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), Ticket [Manager](../saas-apps/ticketmanager-tutorial.md), [Sjabloonkiezer](https://links.officeatwork.com/templatechooser-download-teams)voor Teams , [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](../saas-apps/health-support-system-tutorial.md), [TIJDENS](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md) [,VoerDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [ThingLink voor](https://www.thinglink.com/)docenten en scholen , [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md), [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - februari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud van Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-ondersteuning voor FIDO2-beveiligingssleutels in hybride omgevingen

**Type:** Nieuwe functie  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
We kondigen de openbare preview van Azure AD-ondersteuning voor FIDO2-beveiligingssleutels in hybride omgevingen aan. Gebruikers kunnen nu FIDO2-beveiligingssleutels gebruiken om zich aan te melden bij hun aan Hybrid Azure AD Windows 10-apparaten en naadloze aanmelding te krijgen bij hun on-premises resources en cloudbronnen. Ondersteuning voor hybride omgevingen is de meest aangevraagde functie van onze klanten zonder wachtwoord omdat we in eerste instantie de openbare preview voor FIDO2-ondersteuning hebben gestart op apparaten die zijn toegevoegd aan Azure AD. Verificatie zonder wachtwoord met geavanceerde technologieën zoals biometrie en cryptografie met openbare/persoonlijke sleutels biedt gemak en gebruiksgemak terwijl het veilig is. Met deze openbare preview kunt u nu moderne verificatie, zoals FIDO2-beveiligingssleutels, gebruiken voor toegang tot traditionele Active Directory-resources. Ga voor meer informatie naar [Eenmalige aanmelding bij on-premises resources](../authentication/howto-authentication-passwordless-security-key-on-premises.md). 

Ga naar [FIDO2-beveiligingssleutels inschakelen voor](../authentication/howto-authentication-passwordless-security-key.md) uw tenant voor stapsgewijs instructies om aan de slag te gaan. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>De nieuwe ervaring Mijn account is nu algemeen beschikbaar

**Type:** Functie gewijzigd  
**Servicecategorie:** Mijn profiel/account  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
Mijn account, de enige winkel voor alle behoeften op het gebied van accountbeheer voor eindgebruikers, is nu algemeen beschikbaar. Eindgebruikers hebben toegang tot deze nieuwe site via URL of in de header van de nieuwe Mijn apps ervaring. Meer informatie over alle selfservicemogelijkheden die de nieuwe ervaring biedt, kunt u vinden in Overzicht van [mijn accountportal.](../user-help/my-account-portal-overview.md)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>De URL van mijn accountsite wordt bijgewerkt naar myaccount.microsoft.com

**Type:** Functie gewijzigd  
**Servicecategorie:** Mijn profiel/account  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
De url voor de nieuwe gebruikerservaring Mijn account wordt `https://myaccount.microsoft.com` in de volgende maand bijgewerkt naar . Meer informatie over de ervaring en alle selfservicemogelijkheden voor het account die het biedt aan eindgebruikers vindt u in Help bij de [portal mijn account.](../user-help/my-account-portal-overview.md)

---

## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>De nieuwe Mijn apps-portal is nu algemeen beschikbaar

**Type:** Plannen voor wijziging  
**Servicecategorie:** Mijn apps  
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
Upgrade uw organisatie naar de nieuwe Mijn apps portal die nu algemeen beschikbaar is. Meer informatie over de nieuwe portal en verzamelingen vindt u in [Verzamelingen maken op Mijn apps portal.](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>De naam van werkruimten in Azure AD is gewijzigd in verzamelingen

**Type:** Functie gewijzigd  
**Servicecategorie:** Mijn apps   
**Productmogelijkheid:** Ervaringen van eindgebruikers
 
Werkruimten, de filters die beheerders kunnen configureren om de apps van hun gebruikers te organiseren, worden nu verzamelingen genoemd. Meer informatie over het configureren van deze verzamelingen vindt u in [Verzamelingen maken op Mijn apps portal.](../manage-apps/access-panel-collections.md)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C registreren en aanmelden via telefoon met aangepast beleid (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C
 
Met het registreren en aanmelden van telefoonnummers kunnen ontwikkelaars en ondernemingen hun klanten toestaan zich te registreren en zich aan te melden met een een keer wachtwoord dat via sms naar het telefoonnummer van de gebruiker wordt verzonden. Met deze functie kan de klant ook zijn telefoonnummer wijzigen als hij geen toegang meer heeft tot zijn telefoon. Met de kracht van aangepast beleid en telefonische aanmelding en aanmelding kunnen ontwikkelaars en ondernemingen hun merk communiceren via paginaaanpassing. Meer informatie over het [instellen van telefonische aanmelding](../../active-directory-b2c/phone-authentication-user-flows.md)en aanmelding met aangepast beleid vindt u in Azure AD B2C .
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nieuwe inrichtingsconnectoren in de Azure AD-toepassingsgalerie - januari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden
 
U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - januari 2020

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden
 
In januari 2020 hebben we deze 33 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie: 

[JOSA](../saas-apps/josa-tutorial.md), [Fastly Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](../saas-apps/upshotly-tutorial.md), [LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SmartWork](https://www.intumit.com/teams-smartwork/), [Dotcom-Monitor,](../saas-apps/dotcom-monitor-tutorial.md) [SSOGEN Azure AD SSO-gateway voor Oracle E-Business Suite EBS, PeopleSoft, en JDE](../saas-apps/ssogen-tutorial.md), [Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/)Slogicch Cloud Office365-connector, [PingFlow-verificatie,](https://app-staging.pingview.io/) [ PrinterLogic SaaS,](../saas-apps/printerlogic-saas-tutorial.md) [Taskize Connect,](../saas-apps/taskize-connect-tutorial.md) [Sandwai,](https://app.sandwai.com/) [EZRentOut,](../saas-apps/ezrentout-tutorial.md) [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="two-new-identity-protection-detections"></a>Twee nieuwe identity protection-detecties

**Type:** Nieuwe functie  
**Servicecategorie:** Identity Protection  
**Productmogelijkheid:** Identity Security & Protection
 
We hebben twee nieuwe detectietypen voor gekoppelde aanmelding toegevoegd aan Identity Protection: Verdachte bewerkingsregels voor Postvak IN en Onmogelijk traject. Deze offlinedetecties worden gedetecteerd door Microsoft Cloud App Security (MCAS) en zijn van invloed op het gebruikers- en aanmeldingsrisico in Identity Protection. Zie onze aanmeldingsrisicotypen voor meer informatie over [deze detecties.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Wijziging die de wijziging doorbreekt: URI-fragmenten worden niet uitgevoerd via de aanmeldingsomleiding

**Type:** Functie gewijzigd  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie
 
Vanaf 8 februari 2020, wanneer een aanvraag naar login.microsoftonline.com wordt verzonden om een gebruiker aan te melden, zal de service een leeg fragment aan de aanvraag worden toegezonden.  Dit voorkomt een klasse van omleidingsaanvallen door ervoor te zorgen dat de browser elk bestaand fragment in de aanvraag wist. Geen enkele toepassing mag afhankelijk zijn van dit gedrag. Zie Belangrijke wijzigingen [in](../develop/reference-breaking-changes.md#february-2020) de documentatie van het Microsoft Identity Platform voor meer informatie.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors-inrichting integreren in Azure AD en on-premises AD (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** App-inrichting  
**Productmogelijkheid:** Identity Lifecycle Management

U kunt SAP SuccessFactors nu integreren als een gezaghebbende identiteitsbron in Azure AD. Deze integratie helpt u bij het automatiseren van de end-to-end identiteitslevenscyclus, inclusief het gebruik van op HR gebaseerde gebeurtenissen, zoals nieuwe medewerkers of beëindigingen, om de inrichting van Azure AD-accounts te beheren.

Zie de zelfstudie Automatische inrichting van SAP SuccessFactors configureren voor meer informatie over het instellen van inkomende inrichting van [SAP SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) naar Azure AD.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Ondersteuning voor aangepaste e-mailberichten in Azure AD B2C (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** B2C - Identiteitsbeheer voor consumenten  
**Productmogelijkheid:** B2B/B2C

U kunt nu Azure AD B2C om aangepaste e-mailberichten te maken wanneer uw gebruikers zich registreren om uw apps te gebruiken. Met behulp van DisplayControls (momenteel in preview) en een e-mailprovider van derden (zoals [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)of een aangepaste REST API), kunt u uw eigen e-mailsjabloon,  Van adres en onderwerptekst gebruiken, evenals ondersteuning voor lokalisatie en aangepaste OTP-instellingen (One-Time Password).

Zie Aangepaste e-mailverificatie [in Azure Active Directory B2C.](../../active-directory-b2c/custom-email-sendgrid.md)

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Vervanging van basislijnbeleid door standaardinstellingen voor beveiliging

**Type:** Functie gewijzigd  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

Als onderdeel van een veilig standaardmodel voor verificatie verwijderen we het bestaande basisbeschermingsbeleid van alle tenants. Deze verwijdering is eind februari voltooid. De vervanging voor dit basisbeveiligingsbeleid zijn standaardinstellingen voor beveiliging. Als u basisbeschermingsbeleid hebt gebruikt, moet u van plan zijn om over te gaan op het nieuwe standaardbeleid voor beveiliging of naar voorwaardelijke toegang. Als u deze beleidsregels niet hebt gebruikt, hoeft u geen actie te ondernemen.

Zie Wat zijn standaardinstellingen voor beveiliging? voor meer informatie over [de nieuwe standaardinstellingen voor beveiliging.](./concept-fundamentals-security-defaults.md) Zie Veelvoorkomende beleidsregels voor voorwaardelijke toegang voor meer informatie [over beleid voor voorwaardelijke toegang.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** Plannen voor wijziging  
**Servicecategorie:** Verificaties (aanmeldingen)  
**Productmogelijkheid:** Gebruikersverificatie

Als onderdeel van een veilig standaardmodel voor cookies, verandert de Chrome 80-browser hoe cookies zonder het kenmerk worden `SameSite` behandeld. Elke cookie die het kenmerk niet opgeeft, wordt behandeld alsof deze is ingesteld op , wat ertoe leidt dat Chrome bepaalde scenario's voor het delen van cookies in meerdere domeinen blokkeert waarvan uw app afhankelijk `SameSite` `SameSite=Lax` kan zijn. Als u het oudere Chrome-gedrag wilt behouden, kunt u het kenmerk gebruiken en een extra kenmerk toevoegen, zodat cookies op verschillende plaatsen alleen toegankelijk zijn `SameSite=None` `Secure` via HTTPS-verbindingen. Chrome is gepland om deze wijziging voor 4 februari 2020 te voltooien.

We raden aan dat al onze ontwikkelaars hun apps testen aan de hand van deze richtlijnen:

- Stel de standaardwaarde voor de **instelling Beveiligde cookie gebruiken** in op **Ja.**

- Stel de standaardwaarde voor het **kenmerk SameSite** in op **Geen.**

- Voeg een extra `SameSite` kenmerk van **Secure toe.**

Zie Toekomstige [samesitecookiewijzigingen in ASP.NET](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) en ASP.NET Core en Potentiële onderbreking van websites van klanten en Microsoft-producten en -services in Chrome versie [79](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)en hoger voor meer informatie.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nieuwe hotfix voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** Vaste  
**Servicecategorie:** Microsoft Identity Manager  
**Productmogelijkheid:** Identity Lifecycle Management

Er is een hotfixpakket (build 4.6.34.0) beschikbaar voor Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Dit samentelpakket lost problemen op en voegt verbeteringen toe die worden beschreven in de sectie Problemen opgelost en verbeteringen die in deze update zijn toegevoegd.

Zie updatepakket [van Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)voor meer informatie en om het hotfix-pakket te downloaden.

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nieuw AD FS app-activiteitsrapport om apps te migreren naar Azure AD (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Sso

Gebruik het nieuwe Active Directory Federation Services (AD FS)-app-activiteitenrapport in de Azure Portal om te bepalen welke van uw apps kunnen worden gemigreerd naar Azure AD. Het rapport evalueert alle AD FS apps op compatibiliteit met Azure AD, controleert op problemen en biedt richtlijnen voor het voorbereiden van afzonderlijke apps voor migratie.

Zie Use the AD FS application activity report to migrate applications to Azure AD (Het activiteitenrapport van de toepassing AD FS gebruiken om [toepassingen te migreren naar Azure AD) voor meer informatie.](../manage-apps/migrate-adfs-application-activity.md)

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nieuwe werkstroom voor gebruikers om beheerders toestemming te vragen (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Access Control

De nieuwe werkstroom voor beheerdersgoedkeuring biedt beheerders een manier om toegang te verlenen tot apps waarvoor goedkeuring van de beheerder is vereist. Als een gebruiker toegang probeert te krijgen tot een app, maar geen toestemming kan geven, kan deze nu een aanvraag voor goedkeuring door de beheerder verzenden. De aanvraag wordt per e-mail verzonden en in een wachtrij geplaatst die toegankelijk is vanuit de Azure Portal, naar alle beheerders die zijn aangewezen als beoordelaars. Nadat een revisor actie onderneemt op een aanvraag in behandeling, worden de aanvragende gebruikers op de hoogte gesteld van de actie.

Zie De werkstroom voor beheerders toestemming configureren [(preview) voor meer informatie.](../manage-apps/configure-admin-consent-workflow.md)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nieuwe configuratie Azure AD-app registratie token voor het beheren van optionele claims (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Ontwikkelaarservaring

De nieuwe **configuratieblade** Azure AD-app registratie token op de Azure Portal toont app-ontwikkelaars nu een dynamische lijst met optionele claims voor hun apps. Deze nieuwe ervaring helpt bij het stroomlijnen van migraties van Azure AD-apps en het minimaliseren van onjuiste configuraties van optionele claims.

Zie Optionele claims voor [uw Azure AD-app verstrekken voor meer informatie.](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nieuwe werkstroom voor goedkeuring in twee fases in Azure AD-rechtenbeheer (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Rechtenbeheer

We hebben een nieuwe werkstroom voor goedkeuring in twee fases geïntroduceerd waarmee u twee goedkeurders kunt verplichten om de aanvraag van een gebruiker voor een toegangspakket goed te keuren. U kunt deze bijvoorbeeld zo instellen dat de manager van de aanvragende gebruiker eerst moet goedkeuren, en vervolgens kunt u ook een resource-eigenaar vereisen om goed te keuren. Als een van de goedkeurders dit niet goedkeurt, wordt er geen toegang verleend.

Zie Wijzigingsaanvraag- en [goedkeuringsinstellingen voor een toegangspakket in Azure AD-rechtenbeheer voor meer informatie.](../governance/entitlement-management-access-package-request-policy.md)

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Updates voor de Mijn apps samen met nieuwe werkruimten (openbare preview)

**Type:** Nieuwe functie  
**Servicecategorie:** Mijn apps  
**Productmogelijkheid:** Integratie van derden

U kunt nu de manier aanpassen waarop de gebruikers van uw organisatie de vernieuwde Mijn apps weergeven. Deze nieuwe ervaring bevat ook de functie nieuwe werkruimten, waardoor uw gebruikers gemakkelijker apps kunnen vinden en organiseren.

Zie Werkruimten maken in Mijn apps portal voor meer informatie over de [Mijn apps nieuwe Mijn apps-ervaring](../manage-apps/access-panel-collections.md)en het maken van werkruimten.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google Social ID-ondersteuning voor Azure AD B2B-samenwerking (algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** B2b  
**Productmogelijkheid:** Gebruikersverificatie

Nieuwe ondersteuning voor het gebruik van sociale Google-ID's (Gmail-accounts) in Azure AD maakt samenwerking eenvoudiger voor uw gebruikers en partners. Het is niet langer nodig dat uw partners een nieuw microsoft-specifiek account maken en beheren. Microsoft Teams biedt nu volledige ondersteuning voor Google-gebruikers op alle clients en in de algemene en tenant-gerelateerde verificatie-eindpunten.

Zie Add [Google as an identity provider for B2B guest users (Google toevoegen als id-provider voor B2B-gastgebruikers) voor meer informatie.](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile Support for Conditional Access and Single Sign-on (Algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** Voorwaardelijke toegang  
**Productmogelijkheid:** Identity Security & Protection

Azure AD voor Microsoft Edge iOS en Android ondersteunt nu Azure AD Single Sign-On en voorwaardelijke toegang:

- **Microsoft Edge eenmalige aanmelding (SSO):** Een aanmelding is nu beschikbaar op alle native clients (zoals Microsoft Outlook en Microsoft Edge) voor alle met Azure AD verbonden apps.

- **Microsoft Edge voorwaardelijke toegang:** Via op toepassingen gebaseerd beleid voor voorwaardelijke toegang moeten uw gebruikers Microsoft Intune browsers gebruiken, zoals Microsoft Edge.

Zie de blogpost [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) (Ondersteuning voor voorwaardelijke toegang en eenmalige aanmelding nu algemeen beschikbaar) voor meer informatie over voorwaardelijke toegang en eenmalige aanmelding met Microsoft Edge. Zie Webtoegang beheren met behulp van [](../conditional-access/app-based-conditional-access.md) een browser die [](../conditional-access/require-managed-devices.md)met beleid is beveiligd voor meer informatie over het instellen van uw [client-Microsoft Intune](/intune/apps/app-configuration-managed-browser)apps met op apps gebaseerde voorwaardelijke toegang of voorwaardelijke toegang op basis van apparaten.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-rechtenbeheer (algemene beschikbaarheid)

**Type:** Nieuwe functie  
**Servicecategorie:** Andere  
**Productmogelijkheid:** Rechtenbeheer

Azure AD-rechtenbeheer is een nieuwe functie voor identiteitsbeheer waarmee organisaties de levenscyclus van identiteiten en toegang op schaal kunnen beheren. Deze nieuwe functie helpt bij het automatiseren van werkstromen voor toegangsverzoeken, toegangstoewijzingen, beoordelingen en vervaldatums voor groepen, apps en SharePoint Online-sites.

Met Azure AD-rechtenbeheer kunt u de toegang efficiënter beheren voor werknemers en ook voor gebruikers buiten uw organisatie die toegang tot deze resources nodig hebben.

Zie Wat [is Azure AD-rechtenbeheer? voor meer informatie.](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikersaccounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Integratie van derden  

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [Infor CloudSuite,](../saas-apps/infor-cloudsuite-provisioning-tutorial.md) [OfficeSpace Software,](../saas-apps/officespace-software-provisioning-tutorial.md) [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - november 2019

**Type:** Nieuwe functie  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Integratie van derden

In november 2019 hebben we deze 21 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Airtable](../saas-apps/airtable-tutorial.md), [Hootsuite](../saas-apps/hootsuite-tutorial.md), [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsRapport](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nieuwe en verbeterde Azure AD-toepassingsgalerie

**Type:** Functie gewijzigd  
**Servicecategorie:** Enterprise Apps  
**Productmogelijkheid:** Sso

We hebben de Azure AD-toepassingsgalerie bijgewerkt zodat u eenvoudiger vooraf geïntegreerde apps kunt vinden die ondersteuning bieden voor inrichting, OpenID Connect en SAML in uw Azure Active Directory-tenant.

Zie Een toepassing toevoegen [aan uw Azure Active Directory tenant voor meer informatie.](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Limiet voor de lengte van app-roldefinitie verhoogd van 120 tot 240 tekens

**Type:** Functie gewijzigd  
**Servicecategorie:** Enterprise-apps  
**Productmogelijkheid:** Sso

We hebben van klanten gehoord dat de lengtelimiet voor de waarde van de app-roldefinitie in sommige apps en services te kort is met 120 tekens. Als antwoord hebben we de maximale lengte van de rolwaardedefinitie verhoogd naar 240 tekens.

Zie App-rollen toevoegen in uw toepassing en deze ontvangen in het token voor meer informatie over het gebruik van toepassingsspecifieke [roldefinities.](../develop/howto-add-app-roles-in-azure-ad-apps.md)

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Afschaffing van de identityRiskEvent-API voor Azure AD Identity Protection risicodetecties

**Type:** Plan voor wijziging **van de servicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

In reactie op feedback van ontwikkelaars kunnen Azure AD Premium P2-abonnees nu complexe query's uitvoeren op risicodetectiegegevens van Azure AD Identity Protection met behulp van de nieuwe riskDetection-API voor Microsoft Graph. De bestaande [bètaversie van identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API retourneren geen gegevens meer rond **10 januari 2020.** Als uw organisatie de identityRiskEvent-API gebruikt, moet u overstappen op de nieuwe riskDetection-API.

Zie voor meer informatie over de nieuwe riskDetection-API de referentiedocumentatie [over de API voor risicodetectie.](/graph/api/resources/riskdetection)

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>toepassingsproxy ondersteuning voor het kenmerk SameSite en Chrome 80

**Type:** Servicecategorie **wijzigen plannen: App** **Proxy-productmogelijkheid:** Access Control

Een paar weken vóór de release van de Chrome 80-browser zijn we van plan bij te werken hoe toepassingsproxy cookies omgaan met **het kenmerk SameSite.** Met de release van Chrome 80 wordt elke cookie die niet het **kenmerk SameSite** opgeeft behandeld alsof deze is ingesteld op `SameSite=Lax` .

Om mogelijke negatieve gevolgen als gevolg van deze wijziging te voorkomen, werken we de toepassingsproxy toegang en sessiecookies bij door:

- De standaardwaarde voor de instelling **Beveiligde cookie gebruiken** instellen op **Ja.**

- De standaardwaarde voor het **kenmerk SameSite** instellen op **Geen.**

    >[!NOTE]
    > toepassingsproxy toegangscookies worden altijd uitsluitend verzonden via beveiligde kanalen. Deze wijzigingen zijn alleen van toepassing op sessiecookies.

Zie Cookie settings for [accessing on-premises applications in](../manage-apps/application-proxy-configure-cookie-settings.md)toepassingsproxy (Cookie-instellingen voor toegang tot on-premises toepassingen in Azure Active Directory) voor meer informatie over de Azure Active Directory.

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>App-registraties (verouderd) en app-beheer in de portal voor toepassingsregistratie (apps.dev.microsoft.com) is niet meer beschikbaar

**Type:** Servicecategorie **wijzigen plannen:** **N/A-productmogelijkheid:** Ontwikkelaarservaring

Gebruikers met Azure AD-accounts kunnen toepassingen niet meer registreren of beheren met behulp van de application registration portal (apps.dev.microsoft.com), of toepassingen registreren en beheren in de App-registraties-ervaring (verouderd) in de Azure Portal.

Zie de App-registraties in de Azure Portal voor meer informatie over App-registraties [nieuwe ervaring.](../develop/quickstart-register-app.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers moeten zich niet langer opnieuw registreren tijdens de migratie van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

**Type:** Servicecategorie **opgelost:** **MFA-productmogelijkheid:** Identity Security & Protection

We hebben een bekend probleem opgelost waarbij gebruikers zich opnieuw moesten registreren als ze waren uitgeschakeld voor MFA (Multi-Factor Authentication per gebruiker) en vervolgens werden ingeschakeld voor MFA via een beleid voor voorwaardelijke toegang.

Als u wilt vereisen dat gebruikers zich opnieuw registreren, selecteert u de optie **MFA** opnieuw registreren vereist in de verificatiemethoden van de gebruiker in de Azure AD-portal. Zie Convert [users from per-user MFA to Conditional Access based MFA](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)(Gebruikers van MFA per gebruiker converteren naar MFA op basis van voorwaardelijke toegang) voor meer informatie over het migreren van gebruikers van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nieuwe mogelijkheden voor het transformeren en verzenden van claims in uw SAML-token

**Type:** Nieuwe **functieservicecategorie:** Enterprise Apps **Product capability:** SSO

We hebben extra mogelijkheden toegevoegd om u te helpen bij het aanpassen en verzenden van claims in uw SAML-token. Deze nieuwe mogelijkheden zijn onder andere:

- Aanvullende functies voor claimtransformatie, zodat u de waarde kunt wijzigen die u in de claim verzendt.

- De mogelijkheid om meerdere transformaties toe te passen op één claim.

- Mogelijkheid om de claimbron op te geven, op basis van het gebruikerstype en de groep waarvan de gebruiker deel uitmaken.

Zie Claims aanpassen die zijn uitgegeven in het [SAML-token](../develop/active-directory-saml-claims-customization.md)voor bedrijfstoepassingen voor gedetailleerde informatie over deze nieuwe mogelijkheden, inclusief hoe u deze kunt gebruiken.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nieuwe pagina Mijn aanmeldingen voor eindgebruikers in Azure AD

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product: Bewaking** van & rapportage

We hebben een nieuwe pagina Mijn aanmeldingen toegevoegd ( zodat gebruikers van uw organisatie hun recente **aanmeldingsgeschiedenis** kunnen bekijken om te controleren op https://mysignins.microsoft.com) ongebruikelijke activiteiten. Op deze nieuwe pagina kunnen uw gebruikers het volgende zien:

- Als iemand probeert zijn of haar wachtwoord te raden.

- Als een aanvaller zich heeft aangemeld bij zijn account en vanaf welke locatie.

- Welke apps de aanvaller probeert te openen.

Zie de blog Users [can now check their sign-in history for unusual activity (Gebruikers](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) kunnen nu hun aanmeldingsgeschiedenis controleren op ongebruikelijke activiteitenblog) voor meer informatie.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migratie van Azure AD Domain Services (Azure AD DS) van klassieke naar Azure Resource Manager virtuele netwerken

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services **productfunctie:** Azure AD Domain Services

Voor onze klanten die vast zitten in klassieke virtuele netwerken, hebben we goed nieuws voor u! U kunt nu een een time-migratie uitvoeren van een klassiek virtueel netwerk naar een Resource Manager virtueel netwerk. Nadat u bent overgeschaald naar het Resource Manager virtuele netwerk, kunt u profiteren van de aanvullende en bijgewerkte functies, zoals fijnerf wachtwoordbeleid, e-mailmeldingen en auditlogboeken.

Zie Preview - Migratie van Azure AD Domain Services van het klassieke virtuele netwerkmodel naar [Resource Manager voor meer Resource Manager.](../../active-directory-domain-services/migrate-from-classic-vnet.md)

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Updates voor de Azure AD B2C paginacontractindeling

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

We hebben een aantal nieuwe wijzigingen geïntroduceerd in versie 1.2.0 van het paginacontract voor Azure AD B2C. In deze bijgewerkte versie kunt u nu de laadorder voor uw elementen bepalen, wat ook kan helpen om de hoer te stoppen die zich voordeed wanneer het stijlblad (CSS) wordt geladen.

Zie het versiewijzigingslogboek voor een volledige lijst van de wijzigingen die zijn aangebracht in het [paginacontract.](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Bijwerken naar Mijn apps pagina met nieuwe werkruimten (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **productmogelijkheid:** Access Control

U kunt nu de manier aanpassen waarop gebruikers van uw organisatie de nieuwe Mijn apps-ervaring bekijken en openen, met inbegrip van het gebruik van de functie nieuwe werkruimten om het gemakkelijker voor hen te maken om apps te vinden. De functionaliteit van de nieuwe werkruimten fungeert als filter voor de apps waar gebruikers van uw organisatie al toegang toe hebben.

Zie Werkruimten maken in de portal Mijn apps (preview) voor meer informatie over het uitrollen van de Mijn apps-ervaring en het maken van [werkruimten.](../manage-apps/access-panel-collections.md)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Ondersteuning voor het maandelijks actieve factureringsmodel op basis van gebruikers (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

Azure AD B2C ondersteunt nu facturering van maandelijks actieve gebruikers (MAU). MAU-facturering is gebaseerd op het aantal unieke gebruikers met verificatieactiviteit tijdens een kalendermaand. Bestaande klanten kunnen op elk moment overschakelen naar deze nieuwe factureringsmethode.

Vanaf 1 november 2019 worden alle nieuwe klanten automatisch gefactureerd met deze methode. Deze factureringsmethode biedt klanten voordelen door middel van kostenvoordelen en de mogelijkheid om vooruit te plannen.

Zie Upgrade to [monthly active users billing model (Upgrade uitvoeren naar het](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)factureringsmodel voor maandelijks actieve gebruikers) voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - oktober 2019

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In oktober 2019 hebben we deze 35 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant), [OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4,](https://account.signl4.com/manage) [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Handtekening Point](https://pi.ompnt.com/), Speaking Email for [Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menu-item Geconsolideerde beveiliging in de Azure AD-portal

**Type:** **Functieservicecategorie gewijzigd:** Identity Protection **Product capability:** Identity Security & Protection

U hebt nu toegang tot alle beschikbare Azure AD-beveiligingsfuncties via  het **nieuwe** menu-item Beveiliging en via de zoekbalk in de Azure Portal. Daarnaast bevat de **nieuwe** landingspagina beveiliging, **Security - Getting started,** koppelingen naar onze openbare documentatie, beveiligingsgidsen en implementatiehandleidingen.

Het nieuwe **menu Beveiliging** bevat:

- Voorwaardelijke toegang
- Identiteitsbeveiliging
- Beveiligingscentrum
- Id-veilige score
- Verificatiemethoden
- MFA
- Risicorapporten: riskante gebruikers, riskante aanmeldingen, risicodetecties
- En meer...

Zie Beveiliging - Aan de slag [voor meer informatie.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Verloopbeleid voor Office 365-groepen uitgebreid met autorenewal

**Type:** **Functieservicecategorie gewijzigd: Mogelijkheid** van **groepsbeheerproduct:** Identiteitslevenscyclusbeheer

Het verloopbeleid voor Office 365-groepen is verbeterd om automatisch groepen te vernieuwen die actief worden gebruikt door de leden ervan. Groepen worden automatisch op basis van gebruikersactiviteit in alle Office 365-apps, waaronder Outlook, SharePoint en Teams, automatisch gebruikt.

Deze verbetering helpt bij het verminderen van meldingen over verloop van groepen en helpt ervoor te zorgen dat actieve groepen beschikbaar blijven. Als u al een actief verloopbeleid voor uw Office 365-groepen hebt, hoeft u niets te doen om deze nieuwe functionaliteit in te schakelen.

Zie Het verloopbeleid configureren [voor Office 365-groepen voor meer informatie.](../enterprise-users/groups-lifecycle.md)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Bijgewerkte Azure AD Domain Services (Azure AD DS)

**Type:** **Functieservicecategorie gewijzigd: Azure AD Domain Services** **productfunctie:** Azure AD Domain Services

We hebben de Azure AD Domain Services (Azure AD DS) bijgewerkt met een nieuwe en verbeterde maakervaring, zodat u in slechts drie klikken een beheerd domein kunt maken. Bovendien kunt u nu een sjabloon uploaden en Azure AD DS implementeren.

Zie Zelfstudie: Een exemplaar van Azure Active Directory Domain Services [maken en configureren voor meer informatie.](../../active-directory-domain-services/tutorial-create-instance.md)

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plannen voor wijziging: afschaffing van Power BI inhoudspakketten

**Type:** Servicecategorie wijzigen **plannen: Mogelijkheid** **van rapportageproduct: Bewaking** van & rapportage

Vanaf 1 oktober 2019 Power BI alle inhoudspakketten, inclusief het Azure AD Power BI-inhoudspakket, afgeschaft. Als alternatief voor dit inhoudspakket kunt u Azure AD-werkmappen gebruiken om inzicht te krijgen in uw Azure AD-gerelateerde services. Er zijn extra werkmappen beschikbaar, waaronder werkmappen over beleid voor voorwaardelijke toegang in de modus alleen-rapporteren, inzichten op basis van app-toestemming en meer.

Zie Voor meer informatie over de werkmappen [How to use Azure Monitor workbooks for Azure Active Directory reports .](../reports-monitoring/howto-use-azure-monitor-workbooks.md) Zie de blogpost [Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) (Aankondiging van de algemene beschikbaarheid van sjabloon-apps) voor meer informatie over de afschaffing van de inhoudspakketten.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mijn profiel hernoemt en integreert met de Microsoft Office-accountpagina

**Type:** Servicecategorie **wijzigen plannen: Mijn** profiel-/accountproductmogelijkheid: Samenwerking 

Vanaf oktober wordt mijn profiel mijn account. Als onderdeel van deze wijziging wordt Mijn profiel op alle plaatsen waar dit momenteel **staat,** gewijzigd in **Mijn account.** Naast de naamswijziging en enkele ontwerpverbeteringen biedt de bijgewerkte ervaring extra integratie met de Microsoft Office accountpagina. U hebt met name toegang tot Office-installaties  en -abonnementen via de pagina Overzichtsaccount, samen met office-gerelateerde contactvoorkeuren van de **privacypagina.**

Zie Mijn profiel (preview) portal-overzicht voor meer informatie over de [mijn profiel(preview)-ervaring.](../user-help/my-account-portal-overview.md)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Groepen en leden bulksgewijs beheren met CSV-bestanden in de Azure AD-portal (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Mogelijkheid** voor **groepsbeheerproduct:** Samenwerking

Met trots kondigen we de openbare preview-beschikbaarheid aan van de ervaringen voor groepsbeheer in de Azure AD-portal. U kunt nu een CSV-bestand en de Azure AD-portal gebruiken om groepen en ledenlijsten te beheren, waaronder:

- Leden toevoegen aan of verwijderen uit een groep.

- De lijst met groepen downloaden uit de map .

- De lijst met groepsleden voor een specifieke groep downloaden.

Zie Leden bulksgewijs [toevoegen,](../enterprise-users/groups-bulk-import-members.md) [Leden bulksgewijs verwijderen,](../enterprise-users/groups-bulk-remove-members.md) [Ledenlijst bulksgewijs downloaden](../enterprise-users/groups-bulk-download-members.md)en [Groepenlijst bulksgewijs downloaden voor meer informatie.](../enterprise-users/groups-bulk-download.md)

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamische toestemming wordt nu ondersteund via een nieuw eindpunt voor beheerders toestemming

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

We hebben een nieuw eindpunt voor beheerders toestemming gemaakt ter ondersteuning van dynamische toestemming, wat handig is voor apps die het dynamische toestemmingsmodel op het Microsoft Identity-platform willen gebruiken.

Zie Using the admin consent endpoint (Het eindpunt voor beheerders toestemming gebruiken) voor meer informatie over het gebruik [van dit nieuwe eindpunt.](../develop/v2-admin-consent.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - september 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In september 2019 hebben we deze 29 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office &trade; -](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)Eenmalige aanmelding , [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md) `https://apps.yeeflow.com/` , ARC [Facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), , , `https://app.penneo.com/` `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), Kai [Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), Discovery Benefits [SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-global-reader-role"></a>Nieuwe rol globale lezer van Azure AD

**Type:** Nieuwe **functieservicecategorie:** Azure AD-rollen **Productmogelijkheid:** Access Control

Vanaf 24 september 2019 gaan we een nieuwe rol Azure Active Directory (AD) met de naam Global Reader uitrollen. Deze implementatie begint met productie en global cloudklanten (GCC), die in oktober wereldwijd worden gebeland.

De rol Globale lezer is het alleen-lezen equivalent van globale beheerder. Gebruikers met deze rol kunnen instellingen en beheergegevens in Microsoft 365 lezen, maar kunnen geen beheeracties uitvoeren. We hebben de rol Globale lezer gemaakt om het aantal globale beheerders in uw organisatie te verminderen. Omdat globale beheerdersaccounts krachtig en kwetsbaar zijn voor aanvallen, raden we u aan minder dan vijf globale beheerders te hebben. We raden u aan de rol Globale lezer te gebruiken voor planning, controles of onderzoeken. We raden u ook aan de rol Globale lezer te gebruiken in combinatie met andere beperkte beheerdersrollen, zoals Exchange-beheerder, om werk gedaan te krijgen zonder de rol Globale beheerder te vereisen.

De rol Globale lezer werkt met het nieuwe Microsoft 365-beheercentrum, Exchange-beheercentrum, Teams-beheercentrum, Security Center, Compliance center, Azure AD-beheercentrum en het Beheercentrum voor apparaatbeheer.

>[!NOTE]
> Aan het begin van de openbare preview werkt de rol Globale lezer niet met: SharePoint, Privileged Access Management, Klanten-lockbox, gevoeligheidslabels, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management en Teams App Catalog.

Zie Machtigingen voor beheerdersrol in Azure Active Directory voor [meer Azure Active Directory.](../roles/permissions-reference.md)

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Toegang tot een on-premises rapportserver vanuit uw Power BI - Mobiel app met behulp van Azure Active Directory toepassingsproxy

**Type:** Nieuwe **functieservicecategorie:** App **Proxy-productmogelijkheid:** Access Control

Dankzij de nieuwe integratie tussen de mobiele Power BI-app en Azure AD toepassingsproxy kunt u zich veilig aanmelden bij de mobiele Power BI-app en rapporten van uw organisatie weergeven die worden gehost op de on-premises Power BI Report Server.

Zie de Power BI - Mobiel-site voor meer informatie over Power BI - Mobiel app, waaronder waar u de app [Power BI downloaden.](https://powerbi.microsoft.com/mobile/) Zie Enable remote access to Power BI - Mobiel with Azure AD toepassingsproxy (Externe toegang tot apps inschakelen met Azure AD-toepassingsproxy) voor meer informatie over het instellen van de mobiele app Power BI [Azure AD toepassingsproxy.](../manage-apps/application-proxy-integrate-with-power-bi.md)

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Er is een nieuwe versie van de AzureADPreview PowerShell-module beschikbaar

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Directory

Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module om aangepaste rollen in Azure AD te definiëren en toe te wijzen, waaronder:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nieuwe versie van Azure AD Connect

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Directory

We hebben een bijgewerkte versie van Azure AD Connect voor klanten met automatische upgrades. Deze nieuwe versie bevat verschillende nieuwe functies, verbeteringen en oplossingen voor fouten. Zie voor meer informatie over deze nieuwe versie [Azure AD Connect: releasegeschiedenis van de versie.](../hybrid/reference-connect-version-history.md#14250)

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication-server (MFA), versie 8.0.2 is nu beschikbaar

**Type:** Servicecategorie **opgelost:** **MFA-productmogelijkheid:** Identity Security & Protection

Als u een bestaande klant bent die de MFA-server vóór 1 juli 2019 heeft geactiveerd, kunt u nu de nieuwste versie van MFA Server (versie 8.0.2) downloaden. In deze nieuwe versie doen we het volgende:

- Er is een probleem opgelost. Wanneer Azure AD Sync een gebruiker wijzigt van Uitgeschakeld in Ingeschakeld, wordt er een e-mailbericht naar de gebruiker verzonden.

- Er is een probleem opgelost, zodat klanten een upgrade kunnen uitvoeren, terwijl ze de functionaliteit Tags blijven gebruiken.

- De country-code (+383) is toegevoegd.

- Auditlogboekregistratie voor een een time-bypass is toegevoegd aan MultiFactorAuthSvc.log.

- Verbeterde prestaties voor de webservice-SDK.

- Andere kleine fouten opgelost.

Vanaf 1 juli 2019 biedt Microsoft geen MFA-server meer aan voor nieuwe implementaties. Nieuwe klanten die meervoudige verificatie vereisen, moeten gebruikmaken van Azure AD Multi-Factor Authentication in de cloud. Zie Planning [a cloud-based Azure AD Multi-Factor Authentication deployment (Een cloudimplementatie van Azure AD Multi-Factor Authentication plannen) voor meer informatie.](../authentication/howto-mfa-getstarted.md)

---

## <a name="august-2019"></a>Augustus 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Uitgebreid zoeken, filteren en sorteren voor groepen is beschikbaar in de Azure AD-portal (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Mogelijkheid** voor **groepsbeheerproduct:** Samenwerking

Met trots kondigen we de openbare preview-beschikbaarheid aan van de verbeterde groepen-gerelateerde ervaringen in de Azure AD-portal. Deze verbeteringen helpen u om groepen en ledenlijsten beter te beheren door het volgende te bieden:

- Geavanceerde zoekmogelijkheden, zoals zoeken in subtekenreeksen op groepenlijsten.
- Geavanceerde opties voor filteren en sorteren op lijsten met leden en eigenaars.
- Nieuwe zoekmogelijkheden voor lijsten met leden en eigenaars.
- Nauwkeurigere groepstellingen voor grote groepen.

Zie Groepen beheren [in de](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)Azure Portal.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Er zijn nieuwe aangepaste rollen beschikbaar voor app-registratiebeheer (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Azure AD-rollen **Productmogelijkheid:** Access Control

Aangepaste rollen (beschikbaar met een Azure AD P1- of P2-abonnement) kunnen u nu helpen bij het verkrijgen van specifieke toegang, door u roldefinities met specifieke machtigingen te laten maken en deze rollen vervolgens toe te wijzen aan specifieke resources. Op dit moment maakt u aangepaste rollen met behulp van machtigingen voor het beheren van app-registraties en wijst u de rol vervolgens toe aan een specifieke app. Zie Aangepaste beheerdersrollen in Azure Active Directory [(preview) voor](../roles/custom-overview.md)meer informatie over aangepaste rollen.

Als u aanvullende machtigingen of ondersteunde resources nodig hebt, die u momenteel niet ziet, kunt u feedback verzenden naar onze [Azure-feedbacksite](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) en voegen we uw aanvraag toe aan onze update-routekaart.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nieuwe inrichtingslogboeken kunnen u helpen bij het bewaken en oplossen van problemen met de implementatie van app-inrichting (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** App Provisioning **Product capability:** Identity Lifecycle Management

Er zijn nieuwe inrichtingslogboeken beschikbaar om u te helpen bij het bewaken en oplossen van problemen met de implementatie van gebruikers- en groepsinrichten. Deze nieuwe logboekbestanden bevatten informatie over:

- Welke groepen zijn gemaakt in [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Welke rollen zijn geïmporteerd uit [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Welke werknemers niet zijn geïmporteerd uit [Workday](../saas-apps/workday-inbound-tutorial.md)

Zie Inrichtingsrapporten in de [Azure Active Directory portal (preview) voor meer informatie.](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nieuwe beveiligingsrapporten voor alle Azure AD-beheerders (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

Standaard hebben alle Azure AD-beheerders binnenkort toegang tot moderne beveiligingsrapporten in Azure AD. Tot eind september kunt u de banner boven aan de moderne beveiligingsrapporten gebruiken om terug te keren naar de oude rapporten.

De moderne beveiligingsrapporten bieden aanvullende mogelijkheden van de oudere versies, waaronder:

- Geavanceerd filteren en sorteren
- Bulkacties, zoals het afwijzen van gebruikersrisico's
- Bevestiging van aangetaste of veilige entiteiten
- Risicotoestand, die betrekking heeft op: Risico, Gesloten, Opgelost en Bevestigd gecompromitteerd
- Nieuwe risicogerelateerde detecties (beschikbaar voor Azure AD Premium abonnees)

Zie Riskante gebruikers, [Riskante aanmeldingen](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)en [Risicodetecties voor meer informatie.](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections) [](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Door de gebruiker toegewezen beheerde identiteit is beschikbaar voor Virtual Machines en Virtual Machine Scale Sets (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie: Beheerde** identiteiten voor Azure-resources **Productmogelijkheid:** Ontwikkelaarservaring

Door de gebruiker toegewezen beheerde identiteiten zijn nu algemeen beschikbaar voor Virtual Machines en Virtual Machine Scale Sets. Als onderdeel van dit kan Azure een identiteit maken in de Azure AD-tenant die wordt vertrouwd door het abonnement dat wordt gebruikt en kan worden toegewezen aan een of meer Azure-service-exemplaren. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over door de gebruiker toegewezen [beheerde identiteiten.](../managed-identities-azure-resources/overview.md)

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Gebruikers kunnen hun wachtwoord opnieuw instellen met een mobiele app of hardware-token (algemene beschikbaarheid)

**Type:** **Functieservicecategorie gewijzigd:** Selfservice voor het opnieuw instellen van het **wachtwoord Product:** Gebruikersverificatie

Gebruikers die een mobiele app bij uw organisatie hebben geregistreerd, kunnen nu hun eigen wachtwoord opnieuw instellen door een melding van de Microsoft Authenticator-app goed te keuren of door een code in te voeren vanuit hun mobiele app of hardware-token.

Zie How [it works: Azure AD self-service password reset (Hoe](../authentication/concept-sspr-howitworks.md)werkt het? ) voor meer informatie. Zie Reset your own work or school password overview (Overzicht van uw eigen werk- of schoolwachtwoord opnieuw instellen) voor [meer informatie over de gebruikerservaring.](../user-help/active-directory-passwords-reset-register.md)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET de gedeelde MSAL.NET cache voor on-behalf-of-scenario's genegeerd

**Type:** Opgeloste **servicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Vanaf Azure AD-verificatiebibliotheek (ADAL.NET) versie 5.0.0-preview moeten app-ontwikkelaars één cache per account serialiseren voor [web-apps en web-API's.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) Anders kunnen sommige scenario's die gebruikmaken van de [on-behalf-of-stroom](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) voor Java, samen met een aantal specifieke gebruiksscenario's van , leiden tot verhoging `UserAssertion` van bevoegdheden. Om dit beveiligingsprobleem te voorkomen, ADAL.NET nu de Microsoft Authentication Library voor dotnet (MSAL.NET) gedeelde cache voor on-behalf-of-scenario's genegeerd.

Zie voor meer informatie over dit probleem Azure Active Directory Vulnerability Authentication Library Elevation of Privilege Vulnerability (Beveiligingsprobleem met verhoging van bevoegdheden voor de [verificatiebibliotheek).](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - augustus 2019

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In augustus 2019 hebben we deze 26 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Civic Platform,](../saas-apps/civic-platform-tutorial.md) [Amazon Business,](../saas-apps/amazon-business-tutorial.md) [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox,](../saas-apps/cognidox-tutorial.md) [Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Attendance,](../saas-apps/academy-attendance-tutorial.md) [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliver.media &trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli,](../saas-apps/vocoli-tutorial.md) [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md), [Darwinbox](../saas-apps/darwinbox-tutorial.md), [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Er zijn nieuwe versies van de AzureAD PowerShell- en AzureADPreview PowerShell-modules beschikbaar

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Directory

Er zijn nieuwe updates beschikbaar voor de PowerShell-modules AzureAD en AzureAD Preview:

- Er is `-Filter` een nieuwe parameter toegevoegd aan de parameter in de `Get-AzureADDirectoryRole` AzureAD-module. Met deze parameter kunt u filteren op de directoryrollen die worden geretourneerd door de cmdlet .
- Er zijn nieuwe cmdlets toegevoegd aan de AzureADPreview-module om aangepaste rollen in Azure AD te definiëren en toe te wijzen, waaronder:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Verbeteringen in de gebruikersinterface van de opbouwer voor dynamische groepen in de Azure Portal

**Type:** **Functieservicecategorie gewijzigd: Mogelijkheid** van groepsbeheerproduct: Samenwerking 

We hebben een aantal gebruikersinterfaceverbeteringen aangebracht in de opbouw van dynamische groepen, die beschikbaar zijn in de Azure Portal, om u te helpen een nieuwe regel gemakkelijker in te stellen of bestaande regels te wijzigen. Met deze ontwerpverbetering kunt u regels maken met maximaal vijf expressies, in plaats van slechts één. We hebben ook de lijst met apparaateigenschappen bijgewerkt om afgeschafte apparaateigenschappen te verwijderen.

Zie Dynamische lidmaatschapsregels beheren [voor meer informatie.](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nieuwe machtigingen Microsoft Graph app beschikbaar voor gebruik met toegangsbeoordelingen

**Type:** **Functieservicecategorie gewijzigd: Productmogelijkheid** voor toegangsbeoordelingen: Identiteitsbeheer 

We hebben een nieuwe app-Microsoft Graph, , waarmee apps automatisch toegangsbeoordelingen kunnen maken en ophalen voor groepslidmaatschap en `AccessReview.ReadWrite.Membership` app-toewijzingen. Deze machtiging kan worden gebruikt door uw geplande taken of als onderdeel van uw automatisering, zonder dat hiervoor een aangemelde gebruikerscontext is vereist.

Zie het [PowerShell-blog Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell (Voorbeeld](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)van het maken van Azure AD-toegangsbeoordelingen met behulp Microsoft Graph app-machtigingen met PowerShell) voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-activiteitenlogboeken zijn nu beschikbaar voor cloud-instanties van de overheid in Azure Monitor

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

We zijn blij te kunnen aankondigen dat Azure AD-activiteitenlogboeken nu beschikbaar zijn voor cloud-instanties van de overheid in Azure Monitor. U kunt nu Azure AD-logboeken verzenden naar uw opslagaccount of naar een Event Hub om te integreren met uw SIEM-hulpprogramma's, zoals [Sumologic,](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md) [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)en [ArcSight.](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)

Zie Azure AD-activiteitenlogboeken in Azure Monitor voor meer informatie over het instellen [van Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations)

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Uw gebruikers bijwerken naar de nieuwe, verbeterde ervaring met beveiligingsgegevens

**Type:** **Functieservicecategorie gewijzigd:**  Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Op 25 september 2019 schakelen we de oude, niet-verbeterde beveiligingsgegevenservaring uit voor het registreren en beheren van beveiligingsgegevens van gebruikers en alleen voor het inschakelen van de nieuwe, verbeterde [versie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Dit betekent dat uw gebruikers de oude ervaring niet meer kunnen gebruiken.

Zie onze beheerdersdocumentatie en onze gebruikersdocumentatie voor meer informatie over de verbeterde ervaring met [beveiligingsgegevens.](../user-help/security-info-setup-signin.md) [](../authentication/concept-registration-mfa-sspr-combined.md)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Om deze nieuwe ervaring in te kunnen zetten, moet u het volgende doen:

1. Meld u aan bij Azure Portal globale beheerder of gebruikersbeheerder.

2. Ga naar **Azure Active Directory > gebruikersinstellingen > Instellingen beheren voor preview-functies van het toegangsvenster.**

3. In het gebied Gebruikers kunnen **preview-functies** gebruiken voor het registreren en beheren van beveiligingsgegevens -  uitgebreid selecteert u Geselecteerd **en** kiest u vervolgens een groep gebruikers of kiest u Alle om deze functie in te zetten voor alle gebruikers in de tenant.

4. In het gebied **Gebruikers kunnen preview-functies gebruiken voor het registreren en beheren van beveiliging **info*** selecteert u **Geen.**

5. Sla uw wijzigingen op.

    Nadat u uw instellingen hebt op slaan, hebt u geen toegang meer tot de oude ervaring met beveiligingsgegevens.

>[!Important]
>Als u deze stappen niet voltooit vóór 25 september 2019, wordt uw Azure Active Directory-tenant automatisch ingeschakeld voor de verbeterde ervaring. Als u vragen hebt, kunt u contact met ons opnemen via registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Verificatieaanvragen met POST-aanmeldingen worden strikter gevalideerd

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Standaarden

Vanaf 2 september 2019 worden verificatieaanvragen met behulp van de POST-methode strikter gevalideerd op basis van de HTTP-standaarden. Spaties en dubbele aanhalingstekens (") worden niet meer verwijderd uit aanvraagformulierwaarden. Deze wijzigingen zullen naar verwachting geen bestaande clients breken en helpen ervoor te zorgen dat aanvragen die naar Azure AD worden verzonden, elke keer betrouwbaar worden verwerkt.

Zie azure AD [breaking changes notices (Belangrijke wijzigingen in Azure AD) voor meer informatie.](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plannen voor wijziging: toepassingsproxy service-update om alleen TLS 1.2 te ondersteunen

**Type:** Servicecategorie **wijzigen plannen: App** **Proxy-productmogelijkheid:** Access Control

Om u te helpen onze sterkste versleuteling te bieden, gaan we de toegang tot toepassingsproxy-service beperken tot alleen TLS 1.2-protocollen. Deze beperking wordt in eerste instantie uitgerold voor klanten die al gebruikmaken van TLS 1.2-protocollen, zodat u de gevolgen niet ziet. De TLS 1.0- en TLS 1.1-protocollen worden op 31 augustus 2019 volledig afgeschreven. Klanten die nog steeds TLS 1.0 en TLS 1.1 gebruiken, ontvangen een geavanceerde kennisgeving ter voorbereiding op deze wijziging.

Als u de verbinding met de toepassingsproxy-service gedurende deze wijziging wilt behouden, wordt u aangeraden ervoor te zorgen dat de combinaties client-server en browser-server zijn bijgewerkt voor het gebruik van TLS 1.2. We raden u ook aan om alle clientsystemen op te nemen die door uw werknemers worden gebruikt voor toegang tot apps die zijn gepubliceerd via de toepassingsproxy service.

Zie Een [on-premises](../manage-apps/application-proxy-add-on-premises-application.md)toepassing voor externe toegang toevoegen via een toepassingsproxy in Azure Active Directory voor meer Azure Active Directory.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Wijziging plannen: Er komen ontwerpupdates voor de toepassingsgalerie

**Type:** Servicecategorie **wijzigen plannen: Productmogelijkheid** voor Enterprise **Apps:** SSO

Er zijn nieuwe wijzigingen in de  gebruikersinterface voor het ontwerp van Toevoegen vanuit de galerie van de blade **Een toepassing** toevoegen. Met deze wijzigingen kunt u gemakkelijker uw apps vinden die ondersteuning bieden voor automatische inrichting, OpenID Connect, Security Assertion Markup Language (SAML) en wachtwoord voor eenmalige aanmelding (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Wijziging plannen: het IP-adres van de MFA-server verwijderen uit het IP-adres van Office 365

**Type:** Servicecategorie **wijzigen plannen:** **MFA-productmogelijkheid:** Identity Security & Protection

We verwijderen het IP-adres van de MFA-server uit het IP-adres en de [URL-webservice van Office 365.](/office365/enterprise/office-365-ip-web-service) Als u momenteel afhankelijk bent van deze pagina's om uw firewallinstellingen bij te werken, moet u ervoor zorgen dat u ook de lijst met IP-adressen op de pagina's vermeldt die worden beschreven in de sectie **Firewallvereisten** voor Azure Multi-Factor Authentication-server van het artikel Aan de slag met [azure Multi-Factor Authentication-server.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Voor tokens die alleen voor apps zijn gebruikt, moet de client-app nu bestaan in de resource-tenant

**Type:** Servicecategorie **opgelost:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Op 26 juli 2019 hebben we de manier gewijzigd waarop we alleen-app-tokens bieden via de [clientreferenties verlenen.](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) Voorheen konden apps tokens krijgen om andere apps aan te roepen, ongeacht of de client-app zich in de tenant maakte. We hebben dit gedrag bijgewerkt, zodat resources met één tenant, ook wel web-API's genoemd, alleen kunnen worden aangeroepen door client-apps die bestaan in de resourceten tenant.

Als uw app zich niet in de resource-tenant bevindt, wordt er een foutbericht weergegeven met de tekst ' Om dit probleem op te lossen, moet u de service-principal voor de client-app in de tenant maken met behulp van het eindpunt voor beheerders toestemming of via `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` [PowerShell,](../develop/howto-authenticate-service-principal-powershell.md)zodat uw tenant de app toestemming heeft gegeven om binnen de tenant te werken. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

Zie Wat is er nieuw voor [verificatie? voor meer informatie.](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)

> [!NOTE]
> Bestaande toestemming tussen de client en de API is nog steeds niet vereist. Apps moeten nog steeds hun eigen autorisatiecontroles uitvoeren.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nieuwe aanmelding zonder wachtwoord bij Azure AD met behulp van FIDO2-beveiligingssleutels

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Azure AD-klanten kunnen nu beleid instellen voor het beheren van FIDO2-beveiligingssleutels voor de gebruikers en groepen van hun organisatie. Eindgebruikers kunnen hun beveiligingssleutels ook zelf registreren, de sleutels gebruiken om zich aan te melden bij hun Microsoft-accounts op websites terwijl ze met FIDO kunnen werken, en zich aanmelden bij hun aan Azure AD Windows 10-apparaten.

Zie Aanmelden zonder wachtwoord inschakelen voor [Azure AD (preview)](../authentication/concept-authentication-passwordless.md) voor beheerdersinformatie en Beveiligingsgegevens instellen voor het gebruik van een beveiligingssleutel [(preview)](../user-help/security-info-setup-security-key.md) voor informatie over eindgebruikers voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app galerie - juli 2019

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In juli 2019 hebben we deze 18 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), Clever [Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office &trade; ](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineFact](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikersaccounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe **functieservicecategorie:** **Bedrijfsapps-productmogelijkheid: Bewaking** van & rapportage

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze onlangs geïntegreerde apps automatiseren:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md) (Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van automatische inrichting van gebruikersaccounts

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nieuwe Azure AD Domain Services-servicetag voor netwerkbeveiligingsgroep

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services **productfunctie:** Azure AD Domain Services

Als u geen lange lijsten met IP-adressen en -adresbereiken meer wilt beheren, kunt u de nieuwe netwerkservicetag **AzureActiveDirectoryDomainServices** in uw Azure-netwerkbeveiligingsgroep gebruiken om het inkomende verkeer naar het subnet van uw virtuele Azure AD Domain Services-netwerk te beveiligen.

Zie Netwerkbeveiligingsgroepen voor meer informatie over deze nieuwe [servicetag voor Azure AD Domain Services.](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligingscontroles voor Azure AD Domain Services (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services **productmogelijkheid:** Azure AD Domain Services

Met trots kondigen we de release aan van Azure AD Domain Service Security Auditing naar openbare preview. Beveiligingscontrole biedt u een essentieel inzicht in uw verificatieservices door beveiligingscontrolegebeurtenissen te streamen naar doelresources, waaronder Azure Storage, Azure Log Analytics-werkruimten en Azure Event Hub, met behulp van de Azure AD Domain Service-portal.

Zie Beveiligingscontroles inschakelen voor Azure AD Domain Services [(preview) voor meer informatie.](../../active-directory-domain-services/security-audit-events.md)

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Gebruik van nieuwe verificatiemethoden & inzichten (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Selfservice voor wachtwoord opnieuw **instellen Productmogelijkheid: Bewaking** & rapportage

De nieuwe rapporten gebruik van verificatiemethoden & insights kunnen u helpen te begrijpen hoe functies zoals Azure AD Multi-Factor Authentication en selfservice voor wachtwoord opnieuw instellen worden geregistreerd en gebruikt in uw organisatie, met inbegrip van het aantal geregistreerde gebruikers voor elke functie, hoe vaak selfservice voor wachtwoord opnieuw instellen wordt gebruikt om wachtwoorden opnieuw in te stellen en op welke manier het opnieuw instellen wordt uitgevoerd.

Zie Authentication methods usage & insights (preview) (Gebruik van verificatiemethoden [& insights (preview) ) voor meer informatie.](../authentication/howto-authentication-methods-activity.md)

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Er zijn nieuwe beveiligingsrapporten beschikbaar voor alle Azure AD-beheerders (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

Alle Azure **AD-beheerders** kunnen nu de banner aan de bovenkant  van bestaande beveiligingsrapporten selecteren, zoals het rapport Gebruikers  voor risico's, om te beginnen met het gebruik van de nieuwe beveiligingservaring, zoals wordt weergegeven in de rapporten Riskante gebruikers en Riskante aanmeldingen. Na een periode worden alle beveiligingsrapporten verplaatst van de oudere versies naar de nieuwe versies, met de nieuwe rapporten die u de volgende aanvullende mogelijkheden bieden:

- Geavanceerd filteren en sorteren

- Bulkacties, zoals het afwijzen van gebruikersrisico's

- Bevestiging van aangetaste of veilige entiteiten

- Risicotoestand, die betrekking heeft op: Risico, Gesloten, Opgelost en Bevestigd gecompromitteerd

Zie rapport Riskante gebruikers [en Rapport Riskante](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) [aanmeldingen voor meer informatie.](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nieuwe beveiligingscontroles voor Azure AD Domain Services (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services **productfunctie:** Azure AD Domain Services

Met trots kondigen we de release van Azure AD Domain Service Security Auditing aan voor de openbare preview. Beveiligingscontrole biedt u een essentieel inzicht in uw verificatieservices door beveiligingscontrolegebeurtenissen te streamen naar doelresources, waaronder Azure Storage, Azure Log Analytics-werkruimten en Azure Event Hub, met behulp van de Azure AD Domain Service-portal.

Zie Beveiligingscontroles inschakelen voor Azure AD Domain Services [(preview) voor meer informatie.](../../active-directory-domain-services/security-audit-events.md)

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nieuwe directe B2B-federatie met SAML/WS-Fed (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C

Directe federatie maakt het eenvoudiger voor u om te werken met partners waarvan de it-beheerde identiteitsoplossing niet Azure AD is, door te werken met identiteitssystemen die ondersteuning bieden voor de SAML- of WS-Fed-standaarden. Nadat u een directe federatierelatie met een partner hebt ingesteld, kan elke nieuwe gastgebruiker die u uit dat domein uitnodigt, met u samenwerken met hun bestaande organisatieaccount, waardoor de gebruikerservaring voor uw gasten naadlooser wordt.

Zie Directe federatie met AD FS en externe providers voor [gastgebruikers (preview) voor meer informatie.](../external-identities/direct-federation.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikersaccounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps: Bewaking** van & rapportage

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nieuwe controle op dubbele groepsnamen in de Azure AD-portal

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid van **groepsbeheerproduct:** Samenwerking

Wanneer u nu een groepsnaam maakt of bijvoegt vanuit de Azure AD-portal, wordt er een controle uitgevoerd om te zien of u een bestaande groepsnaam in uw resource dupliceert. Als we bepalen dat de naam al wordt gebruikt door een andere groep, wordt u gevraagd uw naam te wijzigen.

Zie Groepen beheren [in de Azure AD-portal voor meer informatie.](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD ondersteunt nu statische queryparameters in antwoord-URI's (omleiding)

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Azure AD-apps kunnen nu antwoord-URI's (omleiding) registreren en gebruiken met statische queryparameters (bijvoorbeeld ) voor `https://contoso.com/oauth2?idp=microsoft` OAuth 2.0-aanvragen. De statische queryparameter is onderhevig aan tekenreeksen die overeenkomen voor antwoord-URI's, net als elk ander deel van de antwoord-URI. Als er geen geregistreerde tekenreeks is die overeenkomt met de url-decoded redirect-uri, wordt de aanvraag geweigerd. Als de antwoord-URI wordt gevonden, wordt de volledige tekenreeks gebruikt om de gebruiker om te leiden, inclusief de statische queryparameter.

Dynamische antwoord-URI's zijn nog steeds verboden omdat ze een beveiligingsrisico vormen en niet kunnen worden gebruikt om statusinformatie te bewaren in een verificatieaanvraag. Gebruik hiervoor de `state` parameter .

Op dit moment blokkeren de app-registratieschermen van Azure Portal queryparameters nog steeds. U kunt het app-manifest echter handmatig bewerken om queryparameters toe te voegen en te testen in uw app. Zie Wat is er nieuw voor [verificatie? voor meer informatie.](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Activiteitenlogboeken (MS Graph API's) voor Azure AD zijn nu beschikbaar via PowerShell-cmdlets

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Met trots kondigen we aan dat Azure AD-activiteitenlogboeken (audit- en aanmeldingsrapporten) nu beschikbaar zijn via de Azure AD PowerShell-module. Voorheen kon u uw eigen scripts maken met MS Graph API eindpunten en nu hebben we die mogelijkheid uitgebreid naar PowerShell-cmdlets.

Zie Azure [AD PowerShell-cmdlets](../reports-monitoring/reference-powershell-reporting.md)voor rapportage voor meer informatie over het gebruik van deze cmdlets.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Filterbesturingselementen bijgewerkt voor audit- en aanmeldingslogboeken in Azure AD

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

We hebben de audit- en aanmeldingslogboekrapporten bijgewerkt, zodat u nu verschillende filters kunt toepassen zonder dat u ze als kolommen in de rapportschermen moet toevoegen. Daarnaast kunt u nu bepalen hoeveel filters u op het scherm wilt tonen. Deze updates werken allemaal samen om ervoor te zorgen dat uw rapporten gemakkelijker te lezen zijn en beter zijn in te spelen op uw behoeften.

Zie Auditlogboeken filteren en [Aanmeldingsactiviteiten](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) filteren voor meer informatie [over deze updates.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nieuwe riskDetections-API voor Microsoft Graph (openbare preview)

**Type:** Nieuwe **functieServicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

Met trots kondigen we de nieuwe RISKDetections-API voor Microsoft Graph is nu beschikbaar als openbare preview. U kunt deze nieuwe API gebruiken om een lijst weer te geven met aan Identiteitsbeveiliging gerelateerde gebruikers- en aanmeldingsrisicodetecties van uw organisatie. U kunt deze API ook gebruiken om efficiënter query's uit te voeren op risicodetecties, inclusief details over het detectietype, de status, het niveau en meer.

Zie de referentiedocumentatie over de [API voor risicodetectie voor meer informatie.](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - juni 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In juni 2019 hebben we deze 22 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (?塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [In-Car Office](https://me.secure.mercedes-benz.com/), [Smore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager voor Oracle Retail Oracle Access Manager voor Oracle E-Business Suite, Oracle IDCS voor E-Business Suite, Oracle IDCS voor PeopleSoft, Oracle IDCS voor JD Edwards

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Het inrichten van gebruikersaccounts automatiseren voor deze nieuw ondersteunde SaaS-apps

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps: Bewaking** van & rapportage

U kunt nu het maken, bijwerken en verwijderen van gebruikersaccounts voor deze nieuwe geïntegreerde apps automatiseren:

- [In- en uitzoomen](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md) (Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie met behulp van geautomatiseerde inrichting van gebruikersaccounts

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>De realtime voortgang van de Azure AD-inrichtingsservice weergeven

**Type:** **Functieservicecategorie gewijzigd:** App Provisioning **Product capability:** Identity Lifecycle Management

We hebben de inrichtingservaring van Azure AD bijgewerkt met een nieuwe voortgangsbalk die laat zien hoe ver u zich in het inrichtingsproces voor gebruikers beeert. Deze bijgewerkte ervaring biedt ook informatie over het aantal gebruikers dat is ingericht tijdens de huidige cyclus, evenals hoeveel gebruikers er tot nu toe zijn ingericht.

Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) voor meer informatie.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>De huisstijl van het bedrijf wordt nu weergegeven op het scherm voor afmelding en foutmeldingen

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

We hebben Azure AD bijgewerkt zodat de huisstijl van uw bedrijf nu wordt weergegeven op de schermen voor afmelding en foutmeldingen, evenals op de aanmeldingspagina. U hoeft niets te doen om deze functie in te Azure Portal. 

Zie Huisstijl toevoegen aan de [pagina's](./customize-branding.md)van uw organisatie voor meer informatie over het instellen van de huisstijl van Azure Active Directory organisatie.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-server (MFA) is niet meer beschikbaar voor nieuwe implementaties

**Type:** Afgeschafte **servicecategorie:** **MFA-productmogelijkheid:** Identity Security & Protection

Vanaf 1 juli 2019 biedt Microsoft geen MFA-server meer aan voor nieuwe implementaties. Nieuwe klanten die meervoudige verificatie in hun organisatie willen vereisen, moeten nu gebruikmaken van Azure AD Multi-Factor Authentication in de cloud. Klanten die de MFA-server vóór 1 juli hebben geactiveerd, zien geen wijziging. U kunt nog steeds de nieuwste versie downloaden, toekomstige updates downloaden en activeringsreferenties genereren.

Zie Aan de slag met [de Azure-Multi-Factor Authentication-server voor meer Multi-Factor Authentication-server.](../authentication/howto-mfaserver-deploy.md) Zie Planning [a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md)(Een cloudimplementatie van Azure AD Multi-Factor Authentication plannen) voor meer informatie over Azure AD Multi-Factor Authentication in de cloud.

---

## <a name="may-2019"></a>Mei 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Servicewijziging: toekomstige ondersteuning voor alleen TLS 1.2-protocollen in de toepassingsproxy service

**Type:** Servicecategorie **wijzigen plannen:** App **Proxy-productmogelijkheid:** Access Control

Om onze klanten de beste versleuteling te bieden, beperken we de toegang tot alleen TLS 1.2-protocollen op de toepassingsproxy service. Deze wijziging wordt geleidelijk uitgerold voor klanten die al alleen TLS 1.2-protocollen gebruiken. U ziet dus geen wijzigingen.

Afschaffing van TLS 1.0 en TLS 1.1 vindt plaats op 31 augustus 2019, maar we bieden aanvullende geavanceerde kennisgeving, zodat u tijd hebt om u voor te bereiden op deze wijziging. Als voorbereiding op deze wijziging moet u ervoor zorgen dat uw combinaties van client-server en browserserver, inclusief clients die uw gebruikers gebruiken voor toegang tot apps die zijn gepubliceerd via toepassingsproxy, zijn bijgewerkt om het TLS 1.2-protocol te gebruiken om de verbinding met de toepassingsproxy-service te onderhouden. Zie Een [on-premises](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites)toepassing voor externe toegang toevoegen via een toepassingsproxy in Azure Active Directory voor meer Azure Active Directory.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Gebruik het gebruiks- en inzichtrapport om uw app-gerelateerde aanmeldingsgegevens weer te geven

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps: Bewaking** van & rapportage

U kunt nu het rapport over gebruik  en inzichten, dat zich in het gebied Bedrijfstoepassingen van de Azure Portal bevindt, gebruiken om een toepassingsgerichte weergave van uw aanmeldingsgegevens te krijgen, inclusief informatie over:

- Best gebruikte apps voor uw organisatie

- Apps met de meeste mislukte aanmeldingen

- Belangrijkste aanmeldingsfouten voor elke app

Zie Rapport over gebruik en inzichten in de portal voor meer informatie over [Azure Active Directory functie](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Het inrichten van gebruikers voor cloud-apps automatiseren met behulp van Azure AD

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps: Bewaking** van & rapportage

Volg deze nieuwe zelfstudies om de Azure AD Provisioning Service te gebruiken om het maken, verwijderen en bijwerken van gebruikersaccounts voor de volgende cloud-apps te automatiseren:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

U kunt ook deze nieuwe [Dropbox-zelfstudie volgen,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)die informatie biedt over het inrichten van groepsobjecten.

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Gebruikers inrichten voor SaaS-toepassingen automatiseren met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie via geautomatiseerde inrichting van gebruikersaccounts.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Id-veilige score is nu beschikbaar in Azure AD (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie:** **N/A-productmogelijkheid:** Identiteitsbeveiliging & Protection

U kunt nu uw identiteitsbeveiligingsstatus bewaken en verbeteren met behulp van de functie Identity Secure Score in Azure AD. De functie Identity Secure Score maakt gebruik van één dashboard om u te helpen:

- Meet de beveiligingsstatus van uw identiteit op een objectieve manier op basis van een score tussen 1 en 223.

- Verbeteringen in uw identiteitsbeveiliging plannen

- Bekijk het succes van uw beveiligingsverbeteringen

Zie What [is the identity secure score in Azure Active Directory? (Wat is de id-beveiligingsscore in Azure Active Directory? voor meer informatie over de functie id-beveiligingsscore.](./identity-secure-score.md)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nieuwe App-registraties is nu beschikbaar (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid Verificaties **(aanmeldingen) :** Ontwikkelaarservaring

De nieuwe [App-registraties](https://aka.ms/appregistrations) is nu algemeen beschikbaar. Deze nieuwe ervaring bevat alle belangrijke functies die u kent uit de Azure Portal en de portal voor toepassingsregistratie en verbetert deze door:

- **Beter app-beheer.** In plaats van uw apps in verschillende portals te zien, kunt u nu al uw apps op één locatie zien.

- **Vereenvoudigde app-registratie.** Van de verbeterde navigatie-ervaring tot de vernieuwde ervaring voor het selecteren van machtigingen, het is nu eenvoudiger om uw apps te registreren en te beheren.

- **Meer gedetailleerde informatie.** U vindt meer informatie over uw app, waaronder snelstartgidsen en meer.

Zie Microsoft [Identity Platform (Microsoft Identity Platform)](../develop/index.yml) en de [App-registraties ervaring is nu algemeen beschikbaar.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogaankondiging.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nieuwe mogelijkheden die beschikbaar zijn in de API voor riskante gebruikers voor Identity Protection

**Type:** Nieuwe **functieservicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

We zijn blij te kunnen aankondigen dat u nu de API riskante gebruikers kunt gebruiken om de risicogeschiedenis van gebruikers op te halen, riskante gebruikers te sluiten en te bevestigen dat er is gekromd. Deze wijziging helpt u om de risicostatus van uw gebruikers efficiënter bij te werken en inzicht te krijgen in hun risicogeschiedenis.

Zie de referentiedocumentatie over [de API voor riskante gebruikers voor meer informatie.](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - mei 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In mei 2019 hebben we deze 21 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), Quantum [Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Verbeterde ervaringen voor het maken en beheren van groepen in de Azure AD-portal

**Type:** Nieuwe **functieservicecategorie: Mogelijkheid** voor **groepsbeheerproduct:** Samenwerking

We hebben verbeteringen aangebracht in de groepen-gerelateerde ervaringen in de Azure AD-portal. Met deze verbeteringen kunnen beheerders groepenlijsten en ledenlijsten beter beheren en aanvullende opties voor het maken bieden.

De verbeteringen zijn onder andere:

- Basisfiltering op lidmaatschapstype en groepstype.

- Toevoeging van nieuwe kolommen, zoals Bron en E-mailadres.

- Mogelijkheid om groepen, leden en eigenaarslijsten met meerdere selecties te selecteren voor eenvoudige verwijdering.

- Mogelijkheid om een e-mailadres te kiezen en eigenaren toe te voegen tijdens het maken van de groep.

Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](./active-directory-groups-create-azure-portal.md) voor meer informatie.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Een naamgevingsbeleid configureren voor Office 365-groepen in de Azure AD-portal (algemene beschikbaarheid)

**Type:** **Functieservicecategorie gewijzigd: Mogelijkheid** van groepsbeheerproduct: Samenwerking 

Beheerders kunnen nu een naamgevingsbeleid configureren voor Office 365-groepen met behulp van de Azure AD-portal. Deze wijziging helpt bij het afdwingen van consistente naamconventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt naamgevingsbeleid voor Office 365-groepen op twee verschillende manieren configureren:

- Definieer voorvoegsels of achtervoegsels, die automatisch worden toegevoegd aan een groepsnaam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groepsnamen (bijvoorbeeld 'CEO, Salarissen, HR').

Zie Een naamgevingsbeleid [afdwingen voor Office 365-groepen](../enterprise-users/groups-naming-policy.md)voor meer informatie.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-eindpunten zijn nu beschikbaar voor Azure AD-activiteitenlogboeken (algemene beschikbaarheid)

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Met trots kondigen we de algemene beschikbaarheid aan van de ondersteuning Microsoft Graph API-eindpunten voor Azure AD-activiteitenlogboeken. Met deze release kunt u nu versie 1.0 van zowel de Azure AD-auditlogboeken als de API's voor aanmeldingslogboeken gebruiken.

Zie Overzicht van azure [AD-auditlogboek-API voor meer informatie.](/graph/api/resources/azure-ad-auditlog-overview)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Beheerders kunnen nu voorwaardelijke toegang gebruiken voor het gecombineerde registratieproces (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identity Security & Protection

Beheerders kunnen nu beleid voor voorwaardelijke toegang maken voor gebruik door de gecombineerde registratiepagina. Dit omvat het toepassen van beleid om registratie toe te staan als:

- Gebruikers hebben een vertrouwd netwerk.

- Gebruikers lopen een laag aanmeldingsrisico.

- Gebruikers zijn op een beheerd apparaat.

- Gebruikers gaan akkoord met de gebruiksvoorwaarden van de organisatie.

Zie de blogpost Voorwaardelijke toegang voor de gecombineerde registratie van MFA en wachtwoord opnieuw instellen in Azure AD voor meer informatie over voorwaardelijke toegang en wachtwoord [opnieuw instellen.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Zie Beleid voor voorwaardelijke toegang voor gecombineerde registratie voor meer informatie over beleid voor voorwaardelijke toegang voor het [gecombineerde registratieproces.](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration) Zie de functie Azure Active Directory gebruiksvoorwaarden voor meer informatie over [de gebruiksvoorwaarden van Azure AD.](../conditional-access/terms-of-use.md)

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nieuwe Azure AD-bedreigingsinformatie is nu beschikbaar als onderdeel van Azure AD Identity Protection

**Type:** Nieuwe **functieservicecategorie:** Azure AD Identity Protection **productmogelijkheid:** Identity Security & Protection

Azure AD-bedreigingsinformatie detectie is nu beschikbaar als onderdeel van de bijgewerkte Azure AD Identity Protection functie. Deze nieuwe functionaliteit helpt bij het aangeven van ongebruikelijke gebruikersactiviteit voor een specifieke gebruiker of activiteit die consistent is met bekende aanvalspatronen op basis van interne en externe bronnen voor bedreigingsinformatie van Microsoft.

Zie voor meer informatie over de vernieuwde versie van Azure AD Identity Protection de vier belangrijke Azure AD Identity Protection verbeteringen zijn nu in de [openbare preview-blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) en de [Wat is Azure Active Directory Identity Protection (vernieuwd)?](../identity-protection/overview-identity-protection.md) . Zie het artikel Azure AD-bedreigingsinformatie risicodetecties voor meer [Azure Active Directory Identity Protection detectie van](../identity-protection/concept-identity-protection-risks.md) risico's.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD-rechtenbeheer is nu beschikbaar (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Identity **Governance-productmogelijkheid:** Identiteitsbeheer

Azure AD-rechtenbeheer, nu beschikbaar als openbare preview-versie, helpt klanten bij het delegeren van het beheer van toegangspakketten, waarmee wordt bepaald hoe werknemers en zakelijke partners toegang kunnen aanvragen, wie deze moeten goedkeuren en hoe lang ze toegang hebben. Met toegangspakketten kunt u lidmaatschappen beheren in Azure AD- en Office 365-groepen, roltoewijzingen in bedrijfstoepassingen en roltoewijzingen voor SharePoint Online-sites. Lees meer over rechtenbeheer in het [overzicht van Azure AD-rechtenbeheer.](../governance/entitlement-management-overview.md) Zie Wat Azure AD Identity Governance is Azure AD Identity Governance? voor meer informatie over de Azure AD Identity Governance-functies, waaronder Privileged Identity Management, toegangsbeoordelingen en [gebruiksvoorwaarden.](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Een naamgevingsbeleid configureren voor Office 365-groepen in de Azure AD-portal (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Mogelijkheid** voor **groepsbeheerproduct:** Samenwerking

Beheerders kunnen nu een naamgevingsbeleid configureren voor Office 365-groepen met behulp van de Azure AD-portal. Deze wijziging helpt bij het afdwingen van consistente naamconventies voor Office 365-groepen die zijn gemaakt of bewerkt door gebruikers in uw organisatie.

U kunt naamgevingsbeleid voor Office 365-groepen op twee verschillende manieren configureren:

- Definieer voorvoegsels of achtervoegsels, die automatisch worden toegevoegd aan een groepsnaam.

- Upload een aangepaste set geblokkeerde woorden voor uw organisatie, die niet zijn toegestaan in groepsnamen (bijvoorbeeld 'CEO, Salarissen, HR').

Zie Een naamgevingsbeleid [afdwingen voor Office 365-groepen](../enterprise-users/groups-naming-policy.md)voor meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-activiteitenlogboeken zijn nu beschikbaar in Azure Monitor (algemene beschikbaarheid)

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Om uw feedback over visualisaties te kunnen adresseren met de Azure AD-activiteitenlogboeken, introduceren we een nieuwe Insights-functie in Log Analytics. Deze functie helpt u inzicht te krijgen in uw Azure AD-resources door gebruik te maken van onze interactieve sjablonen, Workbooks genaamd. Deze vooraf gebouwde werkmappen kunnen informatie bieden voor apps of gebruikers, en omvatten:

- **Aanmeldingen.** Biedt details voor apps en gebruikers, waaronder de aanmeldingslocatie, het ingebruikte besturingssysteem of de browserclient en -versie, en het aantal geslaagde of mislukte aanmeldingen.

- **Verouderde verificatie en voorwaardelijke toegang.** Biedt details voor apps en gebruikers die gebruikmaken van verouderde verificatie, waaronder Multi-Factor Authentication-gebruik dat wordt geactiveerd door beleidsregels voor voorwaardelijke toegang, apps die gebruikmaken van beleid voor voorwaardelijke toegang, en meer.

- **Analyse van mislukte aanmeldingen.** Helpt u om te bepalen of uw aanmeldingsfouten optreden als gevolg van een gebruikersactie, beleidsproblemen of uw infrastructuur.

- **Aangepaste rapporten.** U kunt nieuwe werkmappen maken of bestaande werkmappen bewerken om de insights-functie voor uw organisatie aan te passen.

Zie How to use Azure Monitor workbooks for Azure Active Directory reports (Werkmappen gebruiken Azure Active Directory [rapporten) voor meer informatie.](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - april 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In april 2019 hebben we deze 21 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md), [Benchling](../saas-apps/benchling-tutorial.md), [MijlIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (eenmalige](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md)aanmelding op basis van rollen) , [Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggogo](https://indiggolead.com/)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nieuwe frequentieoptie voor toegangsbeoordelingen en selectie van meerdere rollen

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor **toegangsbeoordelingen:** Identiteitsbeheer

Met nieuwe updates in Azure AD-toegangsbeoordelingen kunt u het volgende doen:

- Wijzig de frequentie van uw toegangsbeoordelingen in halfjaarlijks, naast de eerder bestaande opties wekelijks, maandelijks, elk kwartaal en jaarlijks.

- Selecteer meerdere Azure AD- en Azure-resourcerollen bij het maken van één toegangsbeoordeling. In dit geval worden alle rollen ingesteld met dezelfde instellingen en worden alle revisoren op hetzelfde moment op de hoogte gesteld.

Zie Een toegangsbeoordeling van groepen of toepassingen maken in Azure AD-toegangsbeoordelingen voor meer informatie over het maken van [een toegangsbeoordeling.](../governance/create-access-review.md)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect een of meer e-mailwaarschuwingen overstappen en voor sommige klanten nieuwe e-mailverzendersinformatie verzenden

**Type:** **Functieservicecategorie gewijzigd: AD Sync** **productfunctie:** Platform

Azure AD Connect bezig met het overstappen van ons e-mailwaarschuwingssysteem(en), waarbij sommige klanten mogelijk een nieuwe afzender van de e-mail zien. Als u dit wilt aanpakken, moet u toevoegen aan de toegestane lijst van uw organisatie, anders kunt u geen belangrijke waarschuwingen meer ontvangen van `azure-noreply@microsoft.com` uw Office 365-, Azure- of synchronisatieservices.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Wijzigingen in upn-achtervoegsels zijn nu geslaagd tussen federatieve domeinen in Azure AD Connect

**Type:** Vaste **servicecategorie:** AD Sync **productmogelijkheid:** Platform

U kunt nu het UPN-achtervoegsel van een gebruiker wijzigen van het ene federatieve domein in een ander federatief domein in Azure AD Connect. Deze oplossing betekent dat het foutbericht FederatedDomainChangeError niet meer moet worden weergegeven tijdens de synchronisatiecyclus of dat u een e-mailmelding ontvangt met de mededeling dat dit object niet kan worden bijgewerkt in Azure Active Directory, omdat het kenmerk [FederatedUser.UserPrincipalName] niet geldig is. Werk de waarde in uw lokale directoryservices bij.

Zie [Troubleshooting Errors during synchronization (Fouten tijdens synchronisatie oplossen) voor meer informatie.](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Verbeterde beveiliging met behulp van het beleid voor voorwaardelijke toegang op basis van app-beveiliging in Azure AD (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identity Security & Protection

App-beveiliging op basis van voorwaardelijke toegang is nu beschikbaar met behulp van **het beleid App-beveiliging** vereisen. Met dit nieuwe beleid kunt u de beveiliging van uw organisatie verbeteren door het volgende te voorkomen:

- Gebruikers krijgen toegang tot apps zonder een Microsoft Intune licentie.

- Gebruikers kunnen geen beveiligingsbeleid voor apps Microsoft Intune krijgen.

- Gebruikers krijgen toegang tot apps zonder een geconfigureerd Microsoft Intune beveiligingsbeleid voor apps.

Zie How to Require app protection policy for cloud app access with Conditional Access (App-beveiligingsbeleid vereisen voor toegang tot [cloud-apps met voorwaardelijke toegang) voor meer informatie.](../conditional-access/app-protection-based-conditional-access.md)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nieuwe ondersteuning voor azure AD-een-op-een-een-aanmelding en voorwaardelijke toegang in Microsoft Edge (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identity Security & Protection

We hebben onze Azure AD-ondersteuning voor Microsoft Edge verbeterd, met inbegrip van nieuwe ondersteuning voor een aanmelding bij Azure AD en voorwaardelijke toegang. Als u eerder een Microsoft Intune Managed Browser gebruikt, kunt u nu in plaats daarvan Microsoft Edge gebruiken.

Zie Beheerde apparaten vereisen voor toegang tot [cloud-apps](../conditional-access/require-managed-devices.md) met voorwaardelijke toegang en Goedgekeurde client-apps vereisen voor toegang tot [cloud-apps](../conditional-access/app-based-conditional-access.md)met voorwaardelijke toegang voor meer informatie over het instellen en beheren van uw apparaten en apps met voorwaardelijke toegang. Zie Internettoegang beheren met een browser Microsoft Intune met beleid voor meer informatie over het beheren van toegang met Microsoft Edge [met Microsoft Intune-beleid.](/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>Maart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework en aangepaste beleidsondersteuning in Azure Active Directory B2C is nu beschikbaar (GA)

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U kunt nu aangepaste beleidsregels maken in Azure AD B2C, met inbegrip van de volgende taken, die op schaal en onder onze Azure SLA worden ondersteund:

- Aangepaste verificatiegebruikerstrajecten maken en uploaden met behulp van aangepast beleid.

- Beschrijf stapsgewijs gebruikerstrajecten als uitwisselingen tussen claimproviders.

- Definieer voorwaardelijke vertakkingen in gebruikerstrajecten.

- Claims transformeren en in kaart brengen voor gebruik in realtime beslissingen en communicatie.

- Gebruik REST API-services in uw aangepaste verificatiegebruikerstrajecten. Bijvoorbeeld met e-mailproviders, CRM's en eigen autorisatiesystemen.

- Federeren met id-providers die compatibel zijn met het OpenIDConnect-protocol. Bijvoorbeeld met Azure AD met meerdere tenants, providers van sociale account of verificatieproviders in twee stappen.

Zie Notities voor ontwikkelaars voor aangepaste beleidsregels [in Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) en lees het blogbericht van Simon, inclusief casestudies, voor meer informatie over het maken van [aangepaste beleidsregels.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nieuwe federatief apps die beschikbaar zijn in de Azure AD-app-galerie - maart 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In maart 2019 hebben we deze 14 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MoetusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nieuwe connectors voor Zscaler- en Atlassian-inrichting in de Azure AD-galerie - maart 2019

**Type:** Nieuwe **functieservicecategorie:** App Provisioning **Product capability:** 3rd Party Integration

Automatiseer het maken, bijwerken en verwijderen van gebruikersaccounts voor de volgende apps:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta,](../saas-apps/zscaler-beta-provisioning-tutorial.md) [Zscaler One,](../saas-apps/zscaler-one-provisioning-tutorial.md) [Zscaler Two,](../saas-apps/zscaler-two-provisioning-tutorial.md) [Zscaler Three,](../saas-apps/zscaler-three-provisioning-tutorial.md) [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Zie [Automate user provisioning to SaaS applications with Azure AD](../app-provisioning/user-provisioning.md)(Automatisch gebruikers inrichten voor SaaS-toepassingen met Azure AD) voor meer informatie over het beter beveiligen van uw organisatie via geautomatiseerde inrichting van gebruikersaccounts.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Verwijderde Office 365-groepen herstellen en beheren in de Azure AD-portal

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid van **groepsbeheerproduct:** Samenwerking

U kunt nu uw verwijderde Office 365-groepen weergeven en beheren vanuit de Azure AD-portal. Met deze wijziging kunt u zien welke groepen beschikbaar zijn om te herstellen, en kunt u ook groepen die niet nodig zijn voor uw organisatie permanent verwijderen.

Zie Verlopen of verwijderde groepen herstellen [voor meer informatie.](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Een aanmelding is nu beschikbaar voor met Azure AD SAML beveiligde on-premises apps via toepassingsproxy (openbare preview)

**Type:** Nieuwe **functieservicecategorie: App** **Proxy-productmogelijkheid:** Access Control

U kunt nu een SSO-ervaring (eenmalige aanmelding) bieden voor on-premises, door SAML geverifieerde apps, samen met externe toegang tot deze apps via toepassingsproxy. Zie SAML-eenmalige aanmelding voor on-premises toepassingen met [toepassingsproxy (preview)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md)voor meer informatie over het instellen van SAML SSO met uw on-premises apps.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Client-apps in aanvraaglussen worden onderbroken om de betrouwbaarheid en gebruikerservaring te verbeteren

**Type:** Nieuwe **functieServicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Client-apps kunnen in korte tijd ten onrechte honderden dezelfde aanmeldingsaanvragen uitgeven. Deze aanvragen dragen allemaal bij aan een slechte gebruikerservaring en verhoogde werkbelastingen voor de IDP, waardoor de latentie voor alle gebruikers toeneemt en de beschikbaarheid van de IDP wordt verkleind.

Deze update verzendt een `invalid_grant` fout: naar client-apps die meerdere keren in korte tijd dubbele aanvragen uitgeven, buiten `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` het bereik van de normale werking. Client-apps die dit probleem ondervinden, moeten een interactieve prompt tonen, waardoor de gebruiker zich opnieuw moet aanmelden. Zie Wat is er nieuw voor [verificatie?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)voor meer informatie over deze wijziging en over het oplossen van deze fout in uw app.

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nieuwe gebruikerservaring voor auditlogboeken is nu beschikbaar

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

We hebben een nieuwe pagina met Auditlogboeken van Azure **AD** gemaakt om de leesbaarheid en de manier waarop u naar uw informatie zoekt te verbeteren. Als u de nieuwe pagina **Auditlogboeken wilt** bekijken, selecteert u **Auditlogboeken** in **de sectie Activiteit** van Azure AD.

![Nieuwe pagina Auditlogboeken, met voorbeeldgegevens](media/whats-new/audit-logs-page.png)

Zie Controleactiviteitenrapporten in de Azure Active Directory **portal** voor meer informatie over [de Azure Active Directory controlelogboeken.](../reports-monitoring/concept-audit-logs.md#audit-logs)

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nieuwe waarschuwingen en richtlijnen om onbedoelde beheerdersvergrendeling van onjuist geconfigureerd beleid voor voorwaardelijke toegang te voorkomen

**Type:** **Functieservicecategorie gewijzigd: Voorwaardelijke** **toegang Productmogelijkheid:** Identity Security & Protection

Om te voorkomen dat beheerders zich per ongeluk buiten hun eigen tenants kunnen vergrendelen via onjuist geconfigureerd beleid voor voorwaardelijke toegang, hebben we nieuwe waarschuwingen en bijgewerkte richtlijnen in de Azure Portal. Zie Wat zijn serviceafhankelijkheden in Azure Active Directory voorwaardelijke toegang voor meer informatie [over de nieuwe richtlijnen.](../conditional-access/service-dependencies.md)

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Verbeterde gebruiksvoorwaarden voor eindgebruikers op mobiele apparaten

**Type:** **Functieservicecategorie gewijzigd: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

We hebben onze bestaande gebruiksvoorwaarden bijgewerkt om de manier waarop u de gebruiksvoorwaarden op een mobiel apparaat beoordeelt en stemt te verbeteren. U kunt nu in- en uitzoomen, teruggaan, de informatie downloaden en hyperlinks selecteren. Zie voor meer informatie over de bijgewerkte [gebruiksvoorwaarden Azure Active Directory gebruiksvoorwaarden functie](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nieuwe downloadervaring voor Azure AD-activiteitenlogboeken beschikbaar

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

U kunt nu grote hoeveelheden activiteitenlogboeken rechtstreeks vanuit de Azure Portal. Met deze update kunt u:

- Download maximaal 250.000 rijen.

- Ontvang een melding nadat het downloaden is voltooid.

- Pas uw bestandsnaam aan.

- Bepaal de uitvoerindeling, JSON of CSV.

Zie Voor meer informatie over deze functie [Quickstart: Een auditrapport downloaden met behulp van de Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Wijziging die problemen veroorzaakt: Updates voor evaluatie van voorwaarden door Exchange ActiveSync (EAS)

**Type:** Plan voor het wijzigen **van de servicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Access Control

We zijn bezig met het bijwerken van de manier waarop Exchange ActiveSync (EAS) de volgende voorwaarden evalueert:

- Gebruikerslocatie, op basis van land, regio of IP-adres

- Aanmeldingsrisico

- Apparaatplatform

Als u deze voorwaarden eerder hebt gebruikt in uw beleid voor voorwaardelijke toegang, moet u er rekening mee houden dat het gedrag van de voorwaarde kan veranderen. Als u bijvoorbeeld eerder de gebruikerslocatievoorwaarde in een beleid hebt gebruikt, kan het zijn dat het beleid nu wordt overgeslagen op basis van de locatie van uw gebruiker.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Configureerbare Azure AD SAML-tokenversleuteling (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Enterprise** Apps **Product capability:** SSO

U kunt nu elke ondersteunde SAML-app configureren voor het ontvangen van versleutelde SAML-tokens. Wanneer azure AD is geconfigureerd en gebruikt met een app, worden de verzonden SAML-asserten versleuteld met behulp van een openbare sleutel die is verkregen van een certificaat dat is opgeslagen in Azure AD.

Zie Configure Azure AD SAML token encryption (Azure AD SAML-tokenversleuteling configureren) voor meer informatie over het configureren van uw [SAML-tokenversleuteling.](../manage-apps/howto-saml-token-encryption.md)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Een toegangsbeoordeling maken voor groepen of apps met behulp van Azure AD-toegangsbeoordelingen

**Type:** Nieuwe **functieServicecategorie: Productmogelijkheid** voor **toegangsbeoordelingen:** Governance

U kunt nu meerdere groepen of apps opnemen in één Azure AD-toegangsbeoordeling voor groepslidmaatschap of app-toewijzing. Toegangsbeoordelingen met meerdere groepen of apps worden ingesteld met dezelfde instellingen en alle opgenomen revisoren worden op hetzelfde moment op de hoogte gesteld.

Zie Een toegangsbeoordeling van groepen of toepassingen maken in Azure AD-toegangsbeoordelingen voor meer informatie over het maken van een toegangsbeoordeling met behulp van [Azure AD-toegangsbeoordelingen](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nieuwe federatief apps beschikbaar in azure AD-app-galerie - februari 2019

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In februari 2019 hebben we deze 27 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle,](../saas-apps/mindtickle-tutorial.md) [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ,](../saas-apps/skyward-qmlativ-tutorial.md) [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile,](../saas-apps/smartfile-tutorial.md) [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [Seismic](../saas-apps/seismic-tutorial.md), Share [A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), Knowledge Anywhere [LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Verbeterde gecombineerde MFA/SSPR-registratie

**Type:** **Functieservicecategorie gewijzigd:** Selfservice voor wachtwoord opnieuw **instellen Productmogelijkheid:** Gebruikersverificatie

In reactie op feedback van klanten hebben we de gecombineerde preview-ervaring voor MFA/SSPR-registratie verbeterd, zodat uw gebruikers hun beveiligingsgegevens sneller kunnen registreren voor zowel MFA als SSPR.

**Volg deze stappen om de verbeterde ervaring voor uw gebruikers vandaag nog in te zetten:**

1. Meld u als globale beheerder of gebruikersbeheerder aan bij de Azure Portal en ga naar Azure Active Directory > Gebruikersinstellingen > Instellingen beheren voor preview-functies van het **toegangsvenster.**

2. In de optie Gebruikers die de **preview-functies** kunnen gebruiken voor het registreren en beheren van beveiligingsgegevens – vernieuwen kiest u de functies in te zetten voor een geselecteerde groep gebruikers **of** **voor Alle gebruikers.**

In de komende weken wordt de mogelijkheid verwijderd om de oude gecombineerde MFA/SSPR-registratievoorbeeldervaring in te zetten voor tenants die deze nog niet hebben ingeschakeld.

**Volg deze stappen om te zien of het besturingselement wordt verwijderd voor uw tenant:**

1. Meld u als globale beheerder of gebruikersbeheerder aan bij de Azure Portal en ga naar Azure Active Directory > Gebruikersinstellingen > Instellingen beheren voor preview-functies van het **toegangsvenster.**

2. Als de **optie Gebruikers die de preview-functies** kunnen gebruiken voor het registreren en beheren van beveiligingsgegevens is ingesteld op **Geen,** wordt de optie verwijderd uit uw tenant.

Ongeacht of u eerder de oude gecombineerde preview-ervaring voor MFA/SSPR-registratie voor gebruikers hebt ingeschakeld, wordt de oude ervaring op een toekomstige datum uitgeschakeld. Daarom raden we u ten zeerste aan om zo snel mogelijk over te gaan op de nieuwe, verbeterde ervaring.

Zie voor meer informatie over de verbeterde registratie-ervaring de cool verbeteringen van de [gecombineerde Azure AD MFA](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)en registratie-ervaring voor wachtwoord opnieuw instellen.

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Bijgewerkte beleidsbeheerervaring voor gebruikersstromen

**Type:** **Functieservicecategorie gewijzigd:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

We hebben het proces voor het maken en beheren van beleid voor gebruikersstromen (voorheen bekend als ingebouwd beleid) eenvoudiger bijgewerkt. Deze nieuwe ervaring is nu de standaardinstelling voor al uw Azure AD-tenants.

U kunt aanvullende feedback en suggesties geven met behulp van de pictogrammen glimlach of fronsen in het gebied **Feedback** verzenden boven aan het portalscherm.

Zie voor meer informatie over de nieuwe ervaring voor beleidsbeheer de Azure AD B2C nu Beschikt over [JavaScript-aanpassing](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) en nog veel meer nieuwe functies blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Specifieke pagina-elementversies kiezen die door de Azure AD B2C

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U kunt nu een specifieke versie kiezen van de pagina-elementen die door de Azure AD B2C. Door een specifieke versie te selecteren, kunt u uw updates testen voordat ze op een pagina worden weergegeven en krijgt u voorspelbaar gedrag. Daarnaast kunt u er nu voor kiezen om specifieke paginaversies af te dwingen om JavaScript-aanpassingen toe te staan. Ga naar de pagina Eigenschappen in uw gebruikersstromen om **deze** functie in te zetten.

Zie voor meer informatie over het kiezen van specifieke versies van pagina-elementen de Azure AD B2C nu [JavaScript-aanpassing](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) en nog veel meer nieuwe functies blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Configureerbare wachtwoordvereisten voor eindgebruikers voor B2C (GA)

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U kunt nu de wachtwoordcomplexiteit van uw organisatie instellen voor uw eindgebruikers, in plaats van uw systeemeigen Azure AD-wachtwoordbeleid te gebruiken. Op **de** blade Eigenschappen van uw gebruikersstromen (voorheen bekend als uw ingebouwde beleidsregels) kunt u de wachtwoordcomplexiteit **Eenvoudig** of **Sterk** kiezen of u kunt een aangepaste **set** vereisten maken.

Zie Configure complexity requirements for passwords in Azure Active Directory B2C voor meer informatie over de configuratie van vereisten [voor wachtwoordcomplexiteit.](../../active-directory-b2c/password-complexity.md)

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nieuwe standaardsjablonen voor aangepaste merkverificatie-ervaringen

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U kunt onze nieuwe standaardsjablonen op de **blade** Pagina-indelingen van uw gebruikersstromen (voorheen bekend als ingebouwd beleid) gebruiken om een aangepaste verificatie-ervaring voor uw gebruikers te maken.

Zie nu Azure AD B2C [JavaScript-aanpassing](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)en nog veel meer nieuwe functies voor meer informatie over het gebruik van de sjablonen.

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samenwerking met behulp van een een time-time wachtwoordcodeverificatie (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C

Er is een een time-time wachtwoordcodeverificatie (OTP) geïntroduceerd voor B2B-gastgebruikers die niet kunnen worden geverifieerd via andere middelen, zoals Azure AD, een Microsoft-account (MSA) of Google-federatie. Deze nieuwe verificatiemethode betekent dat gastgebruikers geen nieuwe verificatiemethode hoeven te Microsoft-account. In plaats daarvan kan een gastgebruiker tijdens het inwisselen van een uitnodiging of het openen van een gedeelde resource een tijdelijke code aanvragen om naar een e-mailadres te worden verzonden. Met deze tijdelijke code kan de gastgebruiker zich blijven aanmelden.

Zie Voor meer informatie e-mailen van een een [time-wachtwoordcodeverificatie (preview)](../external-identities/one-time-passcode.md) en de blog maakt Azure AD het delen en samenwerken naadloos voor elke gebruiker met [elk account.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nieuwe cookie-instellingen toepassingsproxy Azure AD

**Type:** Nieuwe **functieservicecategorie: App** **Proxy-productmogelijkheid:** Access Control

We hebben drie nieuwe cookie-instellingen geïntroduceerd, die beschikbaar zijn voor uw apps die zijn gepubliceerd via toepassingsproxy:

- **Gebruik HTTP-Only cookie.** Hiermee stelt u **de HTTPOnly-vlag** in op toepassingsproxy toegangs- en sessiecookies. Het in-/uitschakelen van deze instelling biedt extra beveiligingsvoordelen, zoals het voorkomen van het kopiëren of wijzigen van cookies via scripting aan clientzijde. U wordt aangeraden deze vlag in te zetten (kies **Ja**) voor de toegevoegde voordelen.

- **Gebruik beveiligde cookie.** Hiermee stelt **u de vlag** Secure in voor toepassingsproxy toegang en sessiecookies. Het in-/uitschakelen van deze instelling biedt extra beveiligingsvoordelen door ervoor te zorgen dat cookies alleen worden verzonden via beveiligde TLS-kanalen, zoals HTTPS. U wordt aangeraden deze vlag in te zetten (kies **Ja**) voor de toegevoegde voordelen.

- **Gebruik permanente cookie.** Hiermee voorkomt u dat toegangscookies verlopen wanneer de webbrowser wordt gesloten. Deze cookies duren de levensduur van het toegangsteken. De cookies worden echter opnieuw ingesteld als de verlooptijd is bereikt of als de gebruiker de cookie handmatig verwijdert. We raden u aan de standaardinstelling **Nee** te gebruiken, alleen de instelling in te stellen voor oudere apps die geen cookies delen tussen processen.

Zie Cookie-instellingen voor toegang tot [on-premises](../manage-apps/application-proxy-configure-cookie-settings.md)toepassingen in Azure Active Directory voor meer informatie over de nieuwe cookies.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - januari 2019

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In januari 2019 hebben we deze 35 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), Vervaldatumherinnering , [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Werkwoord](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md) [](../saas-apps/expiration-reminder-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallAse](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 voor Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nieuwe Azure AD Identity Protection (openbare preview)

**Type:** **Functieservicecategorie gewijzigd:** Identity Protection **Product capability:** Identity Security & Protection

Met trots kondigen we aan dat we de volgende verbeteringen hebben toegevoegd aan de Azure AD Identity Protection preview-versie, waaronder:

- Een bijgewerkte en meer geïntegreerde gebruikersinterface

- Aanvullende API's

- Verbeterde risicoanalyse via machine learning

- Productbrede uitlijning van riskante gebruikers en riskante aanmeldingen

Zie Wat is Azure Active Directory Identity Protection [(vernieuwd)?](../identity-protection/overview-identity-protection.md) voor meer informatie over de verbeteringen. voor meer informatie en om uw ideeën te delen via de prompts binnen het product.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nieuwe App-vergrendeling voor de Microsoft Authenticator-app op iOS- en Android-apparaten

**Type:** Nieuwe **functieservicecategorie: Microsoft Authenticator** **app-productfunctie:** Identiteitsbeveiliging & Protection

Als u uw een time-wachtwoordcodes, app-gegevens en app-instellingen beter wilt beveiligen, kunt u de functie App-vergrendeling in de Microsoft Authenticator-app. Als u App-vergrendeling, wordt u telkens wanneer u de app opent, gevraagd om u te verifiëren met behulp van uw pincode of biometrisch Microsoft Authenticator e gegevens.

Zie veelgestelde vragen over de [Microsoft Authenticator app voor meer informatie.](../user-help/user-help-auth-app-faq.md)

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Uitgebreide exportmogelijkheden voor Azure AD Privileged Identity Management (PIM)

**Type:** Nieuwe **functieservicecategorie:** Privileged Identity Management **productfunctie:** Privileged Identity Management

Privileged Identity Management (PIM) kunnen nu alle actieve en in aanmerking komende roltoewijzingen voor een specifieke resource exporteren, waaronder roltoewijzingen voor alle onderliggende resources. Voorheen was het moeilijk voor beheerders om een volledige lijst met roltoewijzingen voor een abonnement op te halen en moesten ze roltoewijzingen voor elke specifieke resource exporteren.

Zie Activiteiten- en controlegeschiedenis [voor Azure-resourcerollen weergeven in PIM voor meer informatie.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gebruikers die zijn verwijderd uit het synchronisatiebereik, schakelen niet langer over naar cloudaccounts

**Type:** Opgeloste **servicecategorie:** Productmogelijkheid **gebruikersbeheer:** Directory

>[!Important]
>We hebben uw frustraties over deze oplossing gehoord en begrepen. Daarom hebben we deze wijziging teruggedraaid tot het moment dat de oplossing eenvoudiger voor u kan worden geïmplementeerd in uw organisatie.

We hebben een fout opgelost waarbij de vlag DirSyncEnabled van een gebruiker  per asyncrone zou worden overgeschakeld naar False wanneer het Active Directory Domain Services-object (AD DS) werd uitgesloten van het synchronisatiebereik en vervolgens in de volgende synchronisatiecyclus werd verplaatst naar de prullenbak in Azure AD. Als gevolg van deze oplossing, als de gebruiker wordt uitgesloten van het synchronisatiebereik en daarna wordt hersteld vanuit Azure AD prullenbak, blijft het gebruikersaccount zoals verwacht gesynchroniseerd vanuit on-premises AD en kan het niet worden beheerd in de cloud omdat de bron van autoriteit (SoA) blijft zoals on-premises AD.

Vóór deze oplossing was er een probleem toen de vlag DirSyncEnabled werd overgeschakeld naar False. Het heeft de verkeerde indruk gegeven dat deze accounts zijn geconverteerd naar alleen-cloudobjecten en dat de accounts kunnen worden beheerd in de cloud. De accounts behouden hun SoA echter nog steeds als on-premises en alle gesynchroniseerde eigenschappen (schaduwkenmerken) die afkomstig zijn van on-premises AD. Deze situatie heeft geleid tot meerdere problemen in Azure AD en andere cloudworkloads (zoals Exchange Online) die verwachtten dat deze accounts werden behandeld als gesynchroniseerd vanuit AD, maar zich nu als cloudaccounts zouden gedragen.

Op dit moment is de enige manier om een gesynchroniseerd-van-AD-account echt te converteren naar een account dat alleen in de cloud is, door DirSync uit te zetten op tenantniveau, waardoor een back-endbewerking wordt uitgevoerd om de SoA over te dragen. Dit type SoA-wijziging vereist (maar is niet beperkt tot) het ops manieren van alle on-premises gerelateerde kenmerken (zoals LastDirSyncTime en schaduwkenmerken) en het verzenden van een signaal naar andere cloudworkloads, zodat het betreffende object ook wordt geconverteerd naar een cloudaccount.

Deze oplossing voorkomt daarom directe updates op het kenmerk ImmutableID van een gebruiker die is gesynchroniseerd vanuit AD, wat in sommige scenario's in het verleden vereist was. Zoals de naam al aangeeft, is de OnveranderbareID van een object in Azure AD bedoeld om onveranderbaar te zijn. Er zijn nieuwe functies geïmplementeerd in Azure AD Connect Health en Azure AD Connect-synchronisatieclient voor dergelijke scenario's:

- **Grootschalige onveranderbareID-update voor veel gebruikers in een gefaseerd benadering**

  U moet bijvoorbeeld een langdurige migratie AD DS tussen forests. Oplossing: gebruik Azure AD Connect voor  het configureren van bronanker en kopieer, terwijl de gebruiker migreert, de bestaande ImmutableID-waarden van Azure AD naar het kenmerk ms-DS-Consistency-Guid van de lokale AD DS-gebruiker van het nieuwe forest. Zie Using [ms-DS-ConsistencyGuid as sourceAnchor (Ms-DS-ConsistencyGuid gebruiken als sourceAnchor) voor meer informatie.](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)

- **Grootschalige onveranderbareID-updates voor veel gebruikers in één keer**

  Tijdens het implementeren van Azure AD Connect maakt u bijvoorbeeld een fout en nu moet u het kenmerk SourceAnchor wijzigen. Oplossing: Schakel DirSync uit op tenantniveau en schakel alle ongeldige ImmutableID-waarden uit. Zie Directorysynchronisatie uitschakelen [voor Office 365 voor meer informatie.](/office365/enterprise/turn-off-directory-synchronization)

- **On-premises gebruiker opnieuw laten overeenkomen met een bestaande gebruiker in Azure AD** Een gebruiker die bijvoorbeeld opnieuw is gemaakt in AD DS genereert een duplicaat in het Azure AD-account in plaats van deze opnieuw te laten overeenkomen met een bestaand Azure AD-account (zwevend object). Oplossing: gebruik Azure AD Connect Health in de Azure Portal om het bronanker/de onveranderbareid opnieuw toe te wijsen. Zie Zwevend [objectscenario voor meer informatie.](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Wijziging die fouten in de weg staat: werkt het schema voor audit- en aanmeldingslogboeken bij via Azure Monitor

**Type:** **Functieservicecategorie gewijzigd:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Momenteel publiceren we zowel de audit- als aanmeldingslogboekstreams via Azure Monitor, zodat u de logboekbestanden naadloos kunt integreren met uw SIEM-hulpprogramma's of met Log Analytics. Op basis van uw feedback en ter voorbereiding op de aankondiging van de algemene beschikbaarheid van deze functie, brengen we de volgende wijzigingen aan in ons schema. Deze schemawijzigingen en de bijbehorende documentatie-updates vinden plaats in de eerste week van januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nieuwe velden in het auditschema
We voegen een nieuw veld **Bewerkingstype** toe om het type bewerking op te geven dat op de resource wordt uitgevoerd. Bijvoorbeeld **Toevoegen,** **Bijwerken** of **Verwijderen.**

#### <a name="changed-fields-in-the-audit-schema"></a>Velden in het auditschema gewijzigd
De volgende velden worden in het auditschema veranderd:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|Categorie|Dit was het **veld Servicenaam.** Het is nu het **veld Controlecategorieën.** **De naam van** de servicenaam is gewijzigd in **het veld loggedByService.**|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Selfservice voor wachtwoord opnieuw instellen</li></ul>|<ul><li>Gebruikersbeheer</li><li>Groepsbeheer</li><li>App-beheer</li></ul>|
|targetResources|Bevat **TargetResourceType** op het hoogste niveau.|&nbsp;|<ul><li>Beleid</li><li>App</li><li>Gebruiker</li><li>Groep</li></ul>|
|loggedByService|Geeft de naam op van de service die het auditlogboek heeft gegenereerd.|Null|<ul><li>Account inrichten</li><li>Hoofddirectory</li><li>Self-service voor wachtwoord opnieuw instellen</li></ul>|
|Resultaat|Geeft het resultaat van de auditlogboeken. Voorheen werd dit geïnsemaneerd, maar nu wordt de werkelijke waarde weer geven.|<ul><li>0</li><li>1</li></ul>|<ul><li>Geslaagd</li><li>Fout</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Velden in het aanmeldingsschema gewijzigd
De volgende velden worden in het aanmeldingsschema veranderd:

|Veldnaam|Wat is er gewijzigd|Oude waarden|Nieuwe waarden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Dit was **het veld conditionalaccessPolicies.** Het is nu het **veld appliedConditionalAccessPolicies.**|Geen wijziging|Geen wijziging|
|conditionalAccessStatus|Geeft het resultaat weer van de status van het beleid voor voorwaardelijke toegang bij het aanmelden. Voorheen werd dit geïnsemaneerd, maar we geven nu de werkelijke waarde weer.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Geslaagd</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|
|appliedConditionalAccessPolicies: resultaat|Geeft het resultaat van de afzonderlijke status van het beleid voor voorwaardelijke toegang bij het aanmelden. Voorheen werd dit geïnsemaneerd, maar we geven nu de werkelijke waarde weer.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Geslaagd</li><li>Fout</li><li>Niet toegepast</li><li>Uitgeschakeld</li></ul>|

Zie Interpret the Azure [AD audit logs schema in Azure Monitor (preview) (Azure AD-auditlogboekenschema interpreteren in Azure Monitor (preview)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md) voor meer informatie over het schema

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Verbeteringen van Identity Protection in het machine learning-model en de engine voor risicoscores

**Type:** **Functieservicecategorie gewijzigd:** Identity **Protection-productmogelijkheid:** Risicoscores

Verbeteringen in de engine voor gebruikers- en aanmeldingsrisicoanalyse met betrekking tot Identity Protection kunnen helpen om de nauwkeurigheid en dekking van gebruikersrisico's te verbeteren. Beheerders merken mogelijk dat het risiconiveau van gebruikers niet langer rechtstreeks is gekoppeld aan het risiconiveau van specifieke detecties en dat er een toename is in het aantal en het niveau van riskante aanmeldingsgebeurtenissen.

Risicodetecties worden nu geëvalueerd door het machine learning-model onder supervisie, waarmee gebruikersrisico's worden berekend met behulp van aanvullende functies van de aanmeldingen van de gebruiker en een detectiepatroon. Op basis van dit model kan de beheerder gebruikers vinden met hoge risicoscores, zelfs als detecties die aan die gebruiker zijn gekoppeld een laag of gemiddeld risico hebben.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Beheerders kunnen hun eigen wachtwoord opnieuw instellen met de Microsoft Authenticator app (openbare preview)

**Type:** **Functieservicecategorie gewijzigd:** Selfservice voor wachtwoord opnieuw **instellen Productmogelijkheid:** Gebruikersverificatie

Azure AD-beheerders kunnen nu hun eigen wachtwoord opnieuw instellen met behulp van Microsoft Authenticator app-meldingen of een code van een mobiele verificatie-app of hardware-token. Beheerders kunnen nu twee van de volgende methoden gebruiken om hun eigen wachtwoord opnieuw in te stellen:

- Microsoft Authenticator app-melding

- Andere mobiele authenticator-app/Hardware-tokencode

- E-mail

- Telefoongesprek

- Sms-bericht

Zie Selfservice voor wachtwoord opnieuw instellen voor Azure AD - Mobiele app en [SSPR (preview)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr) voor meer informatie over het gebruik van de app Microsoft Authenticator om wachtwoorden opnieuw in te stellen

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nieuwe rol van Azure AD CloudApparaatbeheerder (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** Apparaatregistratie en **-beheer Productmogelijkheid:** Toegangsbeheer

Beheerders kunnen gebruikers toewijzen aan de nieuwe rol Cloudapparaatbeheerder om cloudapparaatbeheerderstaken uit te voeren. Gebruikers aan wie de rol Cloudapparaatbeheerders is toegewezen, kunnen apparaten inSchakelen, uitschakelen en verwijderen in Azure AD, samen met de mogelijkheid om Windows 10 BitLocker-sleutels (indien aanwezig) in de Azure Portal.

Zie Beheerdersrollen toewijzen in Azure Active Directory voor meer informatie [over rollen en machtigingen Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Uw apparaten beheren met behulp van de nieuwe tijdstempel van activiteit in Azure AD (openbare preview)

**Type:** Nieuwe **functieservicecategorie: Apparaatregistratie** en **-beheer Productmogelijkheid:** Levenscyclusbeheer van apparaten

We realiseren ons dat u na een periode de apparaten van uw organisatie in Azure AD moet vernieuwen en in gebruik moet nemen om te voorkomen dat er verouderde apparaten in uw omgeving zijn. Om u te helpen bij dit proces, werkt Azure AD uw apparaten nu bij met een nieuwe tijdstempel van activiteit, zodat u de levenscyclus van uw apparaat kunt beheren.

Zie How To: Manage the verouderde devices in Azure AD (De verouderde apparaten beheren in Azure AD) voor meer informatie over het verkrijgen en gebruiken van [deze tijdstempel.](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Beheerders kunnen vereisen dat gebruikers een gebruiksvoorwaarden accepteren op elk apparaat

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

Beheerders kunnen nu de  optie Vereisen dat gebruikers toestemming geven op elk apparaat intrekken om te vereisen dat uw gebruikers uw gebruiksvoorwaarden accepteren op elk apparaat dat ze in uw tenant gebruiken.

Zie de sectie Gebruiksvoorwaarden per apparaat van de functie Azure Active Directory [gebruiksvoorwaarden voor meer informatie.](../conditional-access/terms-of-use.md#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Beheerders kunnen een gebruiksvoorwaarden zo configureren dat deze verlopen op basis van een terugkerend schema

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance


Beheerders kunnen nu de  optie Toestemmingen verlopen intrekken om een gebruiksvoorwaarden te laten verlopen voor al uw gebruikers op basis van uw opgegeven terugkerende planning. De planning kan jaarlijks, twee jaarlijks, elk kwartaal of maandelijks zijn. Nadat de gebruiksvoorwaarden zijn verlopen, moeten gebruikers deze opnieuw in gebruik hebben.

Zie de sectie Gebruiksvoorwaarden toevoegen van de functie [Azure Active Directory gebruiksvoorwaarden voor meer informatie.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Beheerders kunnen een gebruiksvoorwaarden configureren die verlopen op basis van de planning van elke gebruiker

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

Beheerders kunnen nu een duur opgeven waarin de gebruiker een gebruiksvoorwaarden opnieuw moet instellen. Beheerders kunnen bijvoorbeeld opgeven dat gebruikers een gebruiksvoorwaarden elke 90 dagen opnieuw moeten instellen.

Zie de sectie Gebruiksvoorwaarden toevoegen van de functie [Azure Active Directory gebruiksvoorwaarden voor meer informatie.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nieuwe E Azure AD Privileged Identity Management mails (PIM) voor Azure Active Directory rollen

**Type:** Nieuwe **functieservicecategorie: Privileged Identity Management** **productmogelijkheid:** Privileged Identity Management

Klanten die Azure AD Privileged Identity Management (PIM) gebruiken, kunnen nu een wekelijkse samenvattingsmail ontvangen, met inbegrip van de volgende informatie voor de afgelopen zeven dagen:

- Overzicht van de belangrijkste in aanmerking komende en permanente roltoewijzingen

- Aantal gebruikers dat rollen activeert

- Aantal gebruikers dat is toegewezen aan rollen in PIM

- Aantal gebruikers dat is toegewezen aan rollen buiten PIM

- Aantal gebruikers dat permanent is gemaakt in PIM

Zie E-mailmeldingen in PIM voor meer informatie over PIM en de beschikbare [e-mailmeldingen.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="group-based-licensing-is-now-generally-available"></a>Groepslicenties zijn nu algemeen beschikbaar

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Directory

Groepslicenties zijn niet meer beschikbaar als openbare preview en zijn nu algemeen beschikbaar. Als onderdeel van deze algemene release hebben we deze functie schaalbaarder gemaakt en de mogelijkheid toegevoegd om groepslicentietoewijzingen voor één gebruiker opnieuw te verwerken en de mogelijkheid om groepslicenties te gebruiken met Office 365 E3/A3-licenties.

Zie Wat is groepslicenties in Azure Active Directory? voor meer informatie [over groepslicenties.](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - november 2018

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In november 2018 hebben we deze 26 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveWaardelar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Allaire](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps -](https://test.plexonline.com/signon)Classic Test , Plex Apps – Classic , [Plex](https://www.plexonline.com/signon) [Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX,](https://cloud.plex.com/sso) [Plex Apps – IAM](https://accounts.plex.com/) [,PRESTATIES - Records maken, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md) Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-logboeken werken nu met Azure Log Analytics (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

We zijn blij te kunnen aankondigen dat u nu uw Azure AD-logboeken kunt doorsturen naar Azure Log Analytics. Deze functie die het best wordt aangevraagd, biedt u nog betere toegang tot analyses voor uw bedrijf, bedrijfsactiviteiten en beveiliging, evenals een manier om uw infrastructuur te bewaken. Zie de blog Over activiteitenlogboeken [Azure Active Directory Azure Log Analytics nu beschikbaar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nieuwe federatief apps beschikbaar in azure AD-app-galerie - oktober 2018

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In oktober 2018 hebben we deze 14 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[My Award Points](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive,](../saas-apps/myworkdrive-tutorial.md) [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mailmeldingen

**Type:** Nieuwe **functieservicecategorie:** Azure AD Domain Services **productmogelijkheid:** Azure AD Domain Services

Azure AD Domain Services geeft waarschuwingen over de Azure Portal over onjuiste configuraties of problemen met uw beheerde domein. Deze waarschuwingen bevatten stapsgewijse handleidingen, zodat u kunt proberen de problemen op te lossen zonder contact op te nemen met de ondersteuning.

Vanaf oktober kunt u de meldingsinstellingen voor uw beheerde domein aanpassen. Wanneer er nieuwe waarschuwingen optreden, wordt er een e-mail verzonden naar een aangewezen groep mensen, waardoor de portal niet voortdurend op updates hoeft te worden controleren.

Zie Meldingsinstellingen [in Azure AD Domain Services.](../../active-directory-domain-services/notifications.md)

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>De Azure AD-portal ondersteunt het gebruik van de domein-API ForceDelete om aangepaste domeinen te verwijderen

**Type:** **Functieservicecategorie gewijzigd:** Directory **Management-productmogelijkheid:** Directory

Met trots kondigt u aan dat u nu de domein-API ForceDelete kunt gebruiken om uw aangepaste domeinnamen te verwijderen door de naam van verwijzingen, zoals gebruikers, groepen en apps, asynchroon te wijzigen van uw aangepaste domeinnaam (contoso.com) naar de oorspronkelijke standaarddomeinnaam (contoso.onmicrosoft.com).

Deze wijziging helpt u om uw aangepaste domeinnamen sneller te verwijderen als uw organisatie de naam niet meer gebruikt of als u de domeinnaam met een andere Azure AD moet gebruiken.

Zie Een aangepaste domeinnaam [verwijderen voor meer informatie.](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Machtigingen voor beheerdersrol bijgewerkt voor dynamische groepen

**Type:** Opgeloste **servicecategorie:** Mogelijkheid voor **groepsbeheerproduct:** Samenwerking

We hebben een probleem opgelost, zodat specifieke beheerdersrollen nu dynamische lidmaatschapsregels kunnen maken en bijwerken, zonder dat u de eigenaar van de groep hoeft te zijn.

De rollen zijn:

- Globale beheerder

- Intune-beheerder

- Gebruikersbeheerder

Zie Een dynamische groep maken en de status [controleren voor meer informatie](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Vereenvoudigde configuratie-instellingen Sign-On eenmalige aanmelding (SSO) voor sommige apps van derden

**Type:** Nieuwe **functieservicecategorie: Enterprise** Apps **Product capability:** SSO

We realiseren ons dat het instellen van Single Sign-On (SSO) voor SaaS-apps (Software as a Service) lastig kan zijn vanwege de unieke aard van elke app-configuratie. We hebben een vereenvoudigde configuratie-ervaring gebouwd om de configuratie-instellingen voor eenmalige aanmelding automatisch in te vullen voor de volgende SaaS-apps van derden:

- Zendesk

- ArcGis Online

- Jamf Pro

Als u deze ervaring met één klik wilt gaan gebruiken, gaat u naar **Azure Portal**  >  **configuratiepagina voor eenmalige** aanmelding voor de app. Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md) Azure Active Directory

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory: waar bevinden uw gegevens zich? Pagina

**Type:** Nieuwe **functieservicecategorie: Andere** **productmogelijkheid:** GoLocal

Selecteer de regio van uw bedrijf in **de Azure Active Directory:** waar bevinden uw gegevens zich om weer te geven welk Azure-datacenter uw Azure AD-data-at-rest voor alle Azure AD-services biedt. U kunt de informatie filteren op specifieke Azure AD-services voor de regio van uw bedrijf.

Zie voor toegang tot deze functie en [Azure Active Directory : waar bevinden uw gegevens zich? voor meer informatie.](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Er is een nieuw implementatieplan beschikbaar voor Mijn apps toegangsvenster

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **productfunctie:** SSO

Bekijk het nieuwe implementatieplan dat beschikbaar is voor het Mijn apps-toegangsvenster ( https://aka.ms/deploymentplans) .
Het Mijn apps access biedt gebruikers één plek om hun apps te vinden en te openen. Deze portal biedt gebruikers ook selfservicemogelijkheden, zoals het aanvragen van toegang tot apps en groepen, of het beheren van toegang tot deze resources namens anderen.

Zie Wat is de [Mijn apps portal? voor meer informatie.](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nieuw tabblad Probleemoplossing en ondersteuning op de pagina Aanmeldingslogboeken van de Azure Portal

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Het nieuwe **tabblad Probleemoplossing**  en ondersteuning op de pagina Aanmeldingen van de Azure Portal is bedoeld om beheerders en ondersteuningstechnici te helpen bij het oplossen van problemen met betrekking tot Azure AD-aanmeldingen. Dit nieuwe tabblad bevat de foutcode, foutmelding en herstelaanbevelingen (indien van de hand) om het probleem op te lossen. Als u het probleem niet kunt oplossen, bieden we u ook een  nieuwe manier om een ondersteuningsticket te maken met behulp van de ervaring Kopiëren naar klembord, waarmee de velden Aanvraag-id en -datum **(UTC)** voor het logboekbestand in uw ondersteuningsticket worden ingevuld. 

![Aanmeldingslogboeken met het nieuwe tabblad](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Verbeterde ondersteuning voor aangepaste extensie-eigenschappen die worden gebruikt om dynamische lidmaatschapsregels te maken

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid van **groepsbeheerproduct:** Samenwerking

Met deze update kunt u  nu klikken op de koppeling Aangepaste extensie-eigenschappen downloaden van de opbouwer van dynamische gebruikersgroepregel, uw unieke app-id invoeren en de volledige lijst met aangepaste extensie-eigenschappen ontvangen die u kunt gebruiken bij het maken van een dynamische lidmaatschapsregel voor gebruikers. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app op te halen.

Zie Extensie-eigenschappen en aangepaste extensie-eigenschappen voor meer informatie over het gebruik van aangepaste extensie-eigenschappen voor dynamische [lidmaatschapsregels](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-apps

**Type:** Plannen voor het wijzigen **van de servicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identiteitsbeveiliging en -beveiliging

De volgende apps staan in de lijst met [goedgekeurde client-apps:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft To-Do

- Microsoft Stream

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van Azure AD-apps](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nieuwe ondersteuning voor Self-Service opnieuw instellen van het vergrendelingsscherm van Windows 7/8/8.1

**Type:** Nieuwe **functieservicecategorie:** **SSPR-productmogelijkheid:** Gebruikersverificatie

Nadat u deze nieuwe functie hebt ingesteld, zien uw gebruikers  een koppeling om hun wachtwoord opnieuw in te stellen via het vergrendelingsscherm van een apparaat met Windows 7, Windows 8 of Windows 8.1. Door op die koppeling te klikken, wordt de gebruiker door dezelfde stroom voor het opnieuw instellen van het wachtwoord geleid als via de webbrowser.

Zie How [to enable password reset from Windows 7, 8, and 8.1 (Wachtwoord opnieuw instellen inschakelen vanuit Windows 7, 8 en 8.1)](../authentication/howto-sspr-windows.md) voor meer informatie.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigingsbericht: Autorisatiecodes zijn niet meer beschikbaar voor hergebruik

**Type:** De servicecategorie **Wijzigen plannen:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Vanaf 15 november 2018 accepteert Azure AD geen eerder gebruikte verificatiecodes meer voor apps. Deze beveiligingswijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1- als v2-eindpunten.

Als uw app autorisatiecodes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan de code te gebruiken om een vernieuwingstoken op te halen en dat vernieuwingstoken vervolgens te gebruiken om extra tokens voor andere resources te verkrijgen. Autorisatiecodes kunnen slechts één keer worden gebruikt, maar vernieuwingstokens kunnen meerdere keren worden gebruikt voor meerdere resources. Een app die probeert een verificatiecode opnieuw te gebruiken tijdens de OAuth-codestroom krijgt een invalid_grant foutmelding.

Zie de volledige lijst met nieuwe functies voor verificatie voor deze en andere protocolgerelateerde [wijzigingen.](../develop/reference-breaking-changes.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - september 2018

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In september 2018 hebben we deze 16 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO voor Azure, SurveyMonkey, [Convene](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ondersteuning voor aanvullende claimtransformatiemethoden

**Type:** Nieuwe **functieservicecategorie:** Enterprise Apps **Product capability:** SSO

We hebben nieuwe claimtransformatiemethoden geïntroduceerd, ToLower() en ToUpper(), die kunnen worden toegepast op SAML-tokens vanaf de pagina SamL-gebaseerde **Single Sign-On Configuration.**

Zie How to customize claims issued in the SAML token for enterprise applications in Azure AD (Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen in Azure AD) voor meer informatie.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Bijgewerkte gebruikersinterface voor app-configuratie op basis van SAML (preview)

**Type:** **Functieservicecategorie gewijzigd: Productmogelijkheid** voor Enterprise **Apps:** SSO

Als onderdeel van onze bijgewerkte gebruikersinterface voor app-configuratie op basis van SAML krijgt u het volgende:

- Een bijgewerkte walkthrough-ervaring voor het configureren van uw OP SAML gebaseerde apps.

- Meer inzicht in wat er ontbreekt of onjuist is in uw configuratie.

- De mogelijkheid om meerdere e-mailadressen toe te voegen voor de melding over het verlopen van certificaten.

- Nieuwe claimtransformatiemethoden, ToLower() en ToUpper() en meer.

- Een manier om uw eigen certificaat voor token-ondertekening te uploaden voor uw zakelijke apps.

- Een manier om de NameID-indeling voor SAML-apps in te stellen en een manier om de NameID-waarde in te stellen als Mapextensies.

Als u deze bijgewerkte weergave wilt inloggen, klikt u op de koppeling Onze **nieuwe** ervaring uitproberen boven aan de **pagina Een aanmelding.** Zie [Tutorial: Configure SAML-based single sign-on for an application with Azure Active Directory](../manage-apps/view-applications-portal.md)(Zelfstudie: Een aanmelding op basis van SAML configureren voor een toepassing met Azure Active Directory).

---

## <a name="august-2018"></a>Augustus 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Wijzigingen in Azure Active Directory IP-adresbereiken

**Type:** Plannen voor het wijzigen **van de servicecategorie:** Andere **productmogelijkheid:** Platform

We introduceren grotere IP-bereiken voor Azure AD. Dit betekent dat als u IP-adresbereiken van Azure AD hebt geconfigureerd voor uw firewalls, routers of netwerkbeveiligingsgroepen, u deze moet bijwerken. We maken deze update zodat u de IP-bereikconfiguraties van uw firewall, router of netwerkbeveiligingsgroepen niet opnieuw hoeft te wijzigen wanneer Azure AD nieuwe eindpunten toevoegt.

Netwerkverkeer wordt in de komende twee maanden verplaatst naar deze nieuwe reeksen. Als u wilt doorgaan met een ononderbroken service, moet u deze bijgewerkte waarden vóór 10 september 2018 toevoegen aan uw IP-adressen:

- 20.190.128.0/18

- 40.126.0.0/18

We raden u ten zeerste aan de oude IP-adresbereiken pas te verwijderen als al uw netwerkverkeer naar de nieuwe adresbereiken is verplaatst. Zie [Office 365 URLs and IP address ranges (Office 365-URL's](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)en IP-adresbereiken) voor updates over de overstap en informatie over wanneer u de oude reeksen kunt verwijderen.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Wijzigingsbericht: Autorisatiecodes zijn niet meer beschikbaar voor hergebruik

**Type:** De servicecategorie **Wijzigen plannen:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Vanaf 15 november 2018 accepteert Azure AD geen eerder gebruikte verificatiecodes meer voor apps. Deze beveiligingswijziging helpt Azure AD in overeenstemming te brengen met de OAuth-specificatie en wordt afgedwongen op zowel de v1- als v2-eindpunten.

Als uw app autorisatiecodes opnieuw gebruikt om tokens voor meerdere resources op te halen, raden we u aan de code te gebruiken om een vernieuwingstoken op te halen en dat vernieuwingstoken vervolgens te gebruiken om extra tokens voor andere resources te verkrijgen. Autorisatiecodes kunnen slechts één keer worden gebruikt, maar vernieuwingstokens kunnen meerdere keren worden gebruikt voor meerdere resources. Een app die probeert een verificatiecode opnieuw te gebruiken tijdens de OAuth-codestroom krijgt een invalid_grant fout.

Zie de volledige lijst met nieuwe functies voor verificatie voor deze en andere protocolgerelateerde [wijzigingen.](../develop/reference-breaking-changes.md)

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Geconvergeerd beheer van beveiligingsgegevens voor selfservicewachtwoord (SSPR) en Multi-Factor Authentication (MFA)

**Type:** Nieuwe **functieservicecategorie:** **SSPR-productmogelijkheid:** Gebruikersverificatie

Deze nieuwe functie helpt mensen bij het beheren van hun beveiligingsgegevens (zoals telefoonnummer, mobiele app, e.d.) voor SSPR en MFA op één locatie en ervaring; in vergelijking met eerder, waar dit op twee verschillende locaties is gedaan.

Deze geconvergeerde ervaring werkt ook voor mensen die SSPR of MFA gebruiken. Als uw organisatie bovendien geen MFA- of SSPR-registratie afdwingt, kunnen personen nog steeds methoden voor MFA- of SSPR-beveiligingsgegevens registreren die door uw organisatie zijn toegestaan vanuit de Mijn apps-portal.

Dit is een openbare preview voor opt-in. Beheerders kunnen de nieuwe ervaring (indien gewenst) in ingeschakeld voor een geselecteerde groep of voor alle gebruikers in een tenant. Zie de blog Over geconvergeerde ervaring voor meer informatie over [de geconvergeerde ervaring](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nieuwe instelling HTTP-Only cookies in Azure AD-toepassingsproxy-apps

**Type:** Nieuwe **functieservicecategorie:** App **Proxy-productmogelijkheid:** Access Control

Er is een nieuwe instelling met de naam **HTTP-Only Cookies** in uw toepassingsproxy apps. Deze instelling zorgt voor extra beveiliging door de HTTPOnly-vlag op te nemen in de HTTP-antwoordheader voor zowel toepassingsproxy-toegang als sessiecookies, de toegang tot de cookie te stoppen vanuit een script aan de clientzijde en verdere acties te voorkomen, zoals het kopiëren of wijzigen van de cookie. Hoewel deze vlag nog niet eerder is gebruikt, zijn uw cookies altijd versleuteld en verzonden met behulp van een TLS-verbinding om u te beschermen tegen onjuiste wijzigingen.

Deze instelling is niet compatibel met apps die Gebruikmaken van ActiveX-besturingselementen, zoals Extern bureaublad. Als u in deze situatie zit, raden we u aan deze instelling uit te schakelen.

Zie Toepassingen publiceren met Azure [AD](../manage-apps/application-proxy-add-on-premises-application.md)HTTP-Only voor meer informatie over de instelling toepassingsproxy Cookies.

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) voor Azure-resources ondersteunt resourcetypen van beheergroep

**Type:** Nieuwe **functieservicecategorie:** Privileged Identity Management **productfunctie:** Privileged Identity Management

Just-In-Time-activerings- en toewijzingsinstellingen kunnen nu worden toegepast op resourcetypen van beheergroepen, net zoals u dat al doet voor abonnementen, resourcegroepen en resources (zoals VM's, App Services en meer). Bovendien kan iedereen met een rol die beheerderstoegang biedt voor een beheergroep die resource in PIM ontdekken en beheren.

Zie Azure-resources ontdekken en beheren met behulp van Privileged Identity Management voor meer informatie over PIM- en [Azure-resources Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Toegang tot toepassingen (preview) biedt snellere toegang tot de Azure AD-portal

**Type:** Nieuwe **functieservicecategorie:** Privileged Identity Management **productfunctie:** Privileged Identity Management

Vandaag de dag kan het bij het activeren van een rol met behulp van PIM meer dan tien minuten duren voordat de machtigingen van kracht zijn. Als u ervoor kiest om toepassingstoegang te gebruiken, die momenteel in openbare preview is, hebben beheerders toegang tot de Azure AD-portal zodra de activeringsaanvraag is voltooid.

Op dit moment biedt toegang tot toepassingen alleen ondersteuning voor de Azure AD-portal en Azure-resources. Zie Wat is Azure AD Privileged Identity Management? voor meer informatie over [PIM- en Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nieuwe federatief apps beschikbaar in azure AD-app-galerie - augustus 2018

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Integratie van derden

In augustus 2018 hebben we deze 16 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), Sauce Labs - Mobile [and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), Meta Networks [Connector](../saas-apps/metanetworksconnector-tutorial.md), Way [We Do](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [ProMaster (van Inlogik)](../saas-apps/promaster-tutorial.md), Schoolbooking, [4me](../saas-apps/4me-tutorial.md), [Dossier](../saas-apps/dossier-tutorial.md), [N2F -](../saas-apps/n2f-expensereports-tutorial.md)Onkostendeclaratie , [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Systeemeigen Ondersteuning voor Tableau is nu beschikbaar in Azure AD toepassingsproxy

**Type:** **Functieservicecategorie gewijzigd:** App **Proxy-productmogelijkheid:** Access Control

Met onze update van de OpenID Connect naar het OAuth 2.0 Code Grant-protocol voor ons pre-verificatieprotocol, hoeft u geen aanvullende configuratie meer uit te voeren om Tableau te gebruiken met toepassingsproxy. Deze protocolwijziging helpt u ook toepassingsproxy modernere apps beter te ondersteunen door alleen HTTP-omleidingen te gebruiken, die vaak worden ondersteund in JavaScript- en HTML-tags.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nieuwe ondersteuning voor het toevoegen van Google als id-provider voor B2B-gastgebruikers in Azure Active Directory (preview)

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C

Door federatie met Google in uw organisatie in te stellen, kunt u uitgenodigde Gmail-gebruikers zich laten aanmelden bij uw gedeelde apps en resources met hun bestaande Google-account, zonder dat ze een persoonlijk Microsoft-account (MSP's) of een Azure AD-account moeten maken.

Dit is een openbare preview voor opt-in. Zie Add Google as [an identity provider for B2B guest users (Google toevoegen als id-provider voor B2B-gastgebruikers)](../external-identities/google-federation.md)voor meer informatie over Google-federatie.

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Verbeteringen in Azure Active Directory e-mailmeldingen

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Levenscyclusbeheer van identiteit

Azure Active Directory -e-mailberichten (Azure AD) hebben nu een bijgewerkt ontwerp, evenals wijzigingen in het e-mailadres van de afzender en de weergavenaam van de afzender, wanneer ze worden verzonden vanuit de volgende services:

- Azure AD-toegangsbeoordelingen
- Azure AD Connect Health (Engelstalig)
- Azure AD-identiteitsbeveiliging
- Azure AD Privileged Identity Management
- Meldingen over verlopende certificaten voor bedrijfsapps
- Meldingen voor Enterprise App Provisioning Service

De e-mailmeldingen worden verzonden vanaf het volgende e-mailadres en de weergavenaam:

- E-mailadres: azure-noreply@microsoft.com
- Weergavenaam: Microsoft Azure

Zie E-mailmeldingen [in Azure AD PIM](../privileged-identity-management/pim-email-notifications.md)voor een voorbeeld van een aantal nieuwe e-mailontwerpen en meer informatie.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-activiteitenlogboeken zijn nu beschikbaar via Azure Monitor

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

De Azure AD-activiteitenlogboeken zijn nu beschikbaar in openbare preview voor de Azure Monitor (de platformbrede bewakingsservice van Azure). Azure Monitor biedt naast deze verbeteringen ook een langdurige retentie en naadloze integratie:

- Langetermijnretentie door uw logboekbestanden naar uw eigen Azure-opslagaccount te routeren.

- Naadloze SIEM-integratie, zonder dat u aangepaste scripts moet schrijven of onderhouden.

- Naadloze integratie met uw eigen aangepaste oplossingen, analysehulpprogramma's of oplossingen voor incidentbeheer.

Zie voor meer informatie over deze nieuwe mogelijkheden onze blog Azure AD activity [logs in Azure Monitor diagnostics is now in public preview (Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) AD-activiteitenlogboeken in Azure Monitor diagnostics is nu beschikbaar als openbare preview) en onze [documentatie, Azure Active Directory activity logs in Azure Monitor (preview)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informatie over voorwaardelijke toegang toegevoegd aan het rapport Azure AD-aanmeldingen

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid:** Identity Security & Protection

Met deze update kunt u zien welk beleid wordt geëvalueerd wanneer een gebruiker zich aan meldt, samen met het beleidsresultaat. Daarnaast bevat het rapport nu het type client-app dat door de gebruiker wordt gebruikt, zodat u verouderd protocolverkeer kunt identificeren. Rapportgegevens kunnen nu ook worden doorzocht op een correlatie-id, die te vinden is in het foutbericht en kunnen worden gebruikt om de overeenkomende aanmeldingsaanvraag te identificeren en op te lossen.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Verouderde verificaties weergeven via activiteitenlogboeken voor aanmeldingen

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Met de introductie van het **veld Client-app** in de activiteitenlogboeken voor aanmelding kunnen klanten nu gebruikers zien die gebruikmaken van verouderde verificaties. Klanten hebben toegang tot deze informatie met behulp van de Microsoft Graph-API voor aanmeldingen of via de aanmeldingsactiviteitlogboeken in de Azure AD-portal, waar u het besturingselement **Client-app** kunt gebruiken om te filteren op verouderde verificaties. Raadpleeg de documentatie voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - juli 2018

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In juli 2018 hebben we deze 16 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic](../saas-apps/screencast-tutorial.md), PowerSchool Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](../saas-apps/skillsbase-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nieuwe integraties van SaaS-apps voor het inrichten van gebruikers - juli 2018

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor **app-inrichting:** Integratie van derden

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikersidentiteiten in SaaS-toepassingen zoals Dropbox, Salesforce, ServiceNow en meer automatiseren. Voor juli 2018 hebben we ondersteuning voor het inrichten van gebruikers toegevoegd voor de volgende toepassingen in de Azure AD-app-galerie:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure [AD-galerie.](../saas-apps/tutorial-list.md)

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Verbinding maken met Health for Sync: een eenvoudigere manier om synchronisatiefouten met zwevende en dubbele kenmerken op te lossen

**Type:** Nieuwe **functieservicecategorie:** AD **Connect-productmogelijkheid: bewaking** & rapportage

Azure AD Connect Health introduceert selfservice-herstel om synchronisatiefouten te markeren en op te lossen. Met deze functie worden synchronisatiefouten met gedupliceerde kenmerken opgelost en worden objecten opgelost die zwevend zijn vanuit Azure AD. Deze diagnose heeft de volgende voordelen:

- Beperkt dubbele kenmerksynchronisatiefouten, waardoor specifieke oplossingen worden gevonden

- Er wordt een oplossing toegepast voor toegewezen Azure AD-scenario's, waarin fouten in één stap worden opgelost

- Er is geen upgrade of configuratie vereist om deze functie in te zetten en te gebruiken

Zie Diagnose [and remediate duplicated attribute sync errors (Synchronisatiefouten](../hybrid/how-to-connect-health-diagnose-sync-errors.md) met gedupliceerde kenmerken vaststellen en herstellen) voor meer informatie.

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuele updates voor de aanmeldingservaringen van Azure AD en MSA

**Type:** **Functieservicecategorie gewijzigd: Azure** **AD-productmogelijkheid:** Gebruikersverificatie

We hebben de gebruikersinterface bijgewerkt voor de onlineservices van Microsoft, zoals voor Office 365 en Azure. Door deze wijziging worden de schermen minder rommelig en eenvoudiger. Zie voor meer informatie over deze wijziging de blog Toekomstige verbeteringen van de [Azure AD-aanmeldingservaring.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nieuwe versie van Azure AD Connect - juli 2018

**Type:** **Functieservicecategorie gewijzigd:** App Provisioning **Product capability:** Identity Lifecycle Management

De nieuwste versie van Azure AD Connect omvat:

- Opgeloste fouten en ondersteuningsupdates

- Algemene beschikbaarheid van de Ping-Federate integratie

- Updates voor de nieuwste SQL 2012-client

Zie voor meer informatie over deze update [Azure AD Connect: Releasegeschiedenis van versie](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Updates van de gebruiksvoorwaarden voor eindgebruikers

**Type:** **Functieservicecategorie gewijzigd: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

We werken de acceptatiereeks bij in de gebruikersinterface van tou-eindgebruikers.

**Huidige tekst.** Als u toegang wilt krijgen tot [tenantName]-resources, moet u de gebruiksvoorwaarden accepteren.<br>**Nieuwe tekst.** Als u toegang wilt krijgen tot de resource [tenantName], moet u de gebruiksvoorwaarden lezen.

**Huidige tekst:** Kiezen om te accepteren betekent dat u akkoord gaat met alle bovenstaande gebruiksvoorwaarden.<br>**Nieuwe tekst:** Klik op Accepteren om te bevestigen dat u de gebruiksvoorwaarden hebt gelezen en begrepen.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Pass-through-verificatie ondersteunt verouderde protocollen en toepassingen

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Pass-through-verificatie ondersteunt nu verouderde protocollen en apps. De volgende beperkingen worden nu volledig ondersteund:

- Aanmeldingen van gebruikers bij oudere Office-clienttoepassingen, Office 2010 en Office 2013, zonder dat moderne verificatie is vereist.

- Toegang tot agenda delen en informatie over gratis/bezet in hybride Exchange-omgevingen alleen in Office 2010.

- Aanmeldingen van gebruikers bij clienttoepassingen van Skype voor Bedrijven zonder moderne verificatie.

- Gebruikers melden zich aan bij PowerShell versie 1.0.

- De Apple Device Enrollment Program (Apple DEP) met behulp van de iOS-configuratieassistent.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Geconvergeerd beheer van beveiligingsgegevens voor selfservice voor wachtwoord opnieuw instellen en Multi-Factor Authentication

**Type:** Nieuwe **functieservicecategorie:** **SSPR-productmogelijkheid:** Gebruikersverificatie

Met deze nieuwe functie kunnen gebruikers hun beveiligingsgegevens (bijvoorbeeld telefoonnummer, e-mailadres, mobiele app, en meer) voor selfservice voor wachtwoord opnieuw instellen (SSPR) en Multi-Factor Authentication (MFA) in één ervaring beheren. Gebruikers hoeft niet langer dezelfde beveiligingsgegevens voor SSPR en MFA in twee verschillende ervaringen te registreren. Deze nieuwe ervaring geldt ook voor gebruikers met SSPR of MFA.

Als een organisatie MFA of SSPR-registratie **niet afdwingt,** kunnen gebruikers hun beveiligingsgegevens registreren via Mijn apps portal. Hier kunnen gebruikers alle methoden registreren die zijn ingeschakeld voor MFA of SSPR.

Dit is een openbare preview-versie voor opt-in. Beheerders kunnen de nieuwe ervaring in (indien gewenst) in te zetten voor een geselecteerde groep gebruikers of alle gebruikers in een tenant.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>De app Microsoft Authenticator gebruiken om uw identiteit te verifiëren wanneer u uw wachtwoord opnieuw in stelt

**Type:** **Functieservicecategorie gewijzigd:** SSPR-productmogelijkheid: Gebruikersverificatie 

Met deze functie kunnen niet-beheerders hun identiteit verifiëren tijdens het opnieuw instellen van een wachtwoord met behulp van een melding of code van Microsoft Authenticator (of een andere ver authenticator-app). Nadat beheerders deze selfservicemethode voor wachtwoord opnieuw instellen hebben ingesteld, kunnen gebruikers die een mobiele app hebben geregistreerd via aka.ms/mfasetup of aka.ms/setupsecurityinfo hun mobiele app gebruiken als verificatiemethode bij het opnieuw instellen van hun wachtwoord.

Meldingen van mobiele apps kunnen alleen worden ingeschakeld als onderdeel van een beleid dat twee methoden vereist om uw wachtwoord opnieuw in te stellen.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Wijzigingsbericht: Beveiligingsfix voor de gedelegeerde autorisatiestroom voor apps met behulp van de API voor Azure AD-activiteitenlogboeken

**Type:** Servicecategorie wijzigen **plannen: Mogelijkheid** **van rapportageproduct: Bewaking** van & rapportage

Vanwege onze sterkere beveiligingshandhaving hebben we een wijziging moeten maken in de machtigingen voor apps die gebruikmaken van een gedelegeerde autorisatiestroom voor toegang tot API's voor [Azure AD-activiteitenlogboeken.](../reports-monitoring/concept-reporting-api.md) Deze wijziging vindt plaats op **26 juni 2018.**

Als een van uw apps gebruik maakt van Api's voor Activiteitenlogboek van Azure AD, volgt u deze stappen om ervoor te zorgen dat de app niet wordt ingingakt nadat de wijziging is uitgevoerd.

**Uw app-machtigingen bijwerken**

1. Meld u aan bij de Azure Portal, **selecteer Azure Active Directory** en selecteer **vervolgens App-registraties.**
2. Selecteer uw app die gebruikmaakt van de API voor Azure AD-activiteitenlogboeken, selecteer **Instellingen,** selecteer Vereiste machtigingen en selecteer vervolgens de **Windows Azure Active Directory** API.
3. Schakel in **het gebied Gedelegeerde** machtigingen van de blade Toegang **inschakelen** het selectievakje in naast Mapgegevens **lezen** en selecteer **vervolgens Opslaan.**
4. Selecteer **Machtigingen verlenen** en selecteer vervolgens **Ja.**

    >[!Note]
    >U moet een Globale beheerder om machtigingen te verlenen aan de app.

Zie het gebied [](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) Machtigingen verlenen in het artikel Vereisten voor toegang tot de rapportage-API van Azure AD voor meer informatie.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>TLS-instellingen configureren om verbinding te maken met Azure AD-services voor PCI DSS naleving

**Type:** Nieuwe **functieservicecategorie:** **N/A-productmogelijkheid:** Platform

Transport Layer Security (TLS) is een protocol dat privacy en gegevensintegriteit biedt tussen twee communicerende toepassingen en het meest geïmplementeerde beveiligingsprotocol is dat momenteel wordt gebruikt.

De [PCI Security Standards Door](https://www.pcisecuritystandards.org/) heeft vastgesteld dat vroege versies van TLS en Secure Sockets Layer (SSL) moeten worden uitgeschakeld ten voordele van het inschakelen van nieuwe en veiligere app-protocollen, met naleving vanaf 30 juni **2018.** Deze wijziging betekent dat als u verbinding maakt met Azure AD-services en naleving PCI DSS, U TLS 1.0 moet uitschakelen. Er zijn meerdere versies van TLS beschikbaar, maar TLS 1.2 is de nieuwste versie die beschikbaar is voor Azure Active Directory Services. We raden u ten zeerste aan om rechtstreeks naar TLS 1.2 te gaan voor combinaties van client/server en browser/server.

Out-of-date browsers bieden mogelijk geen ondersteuning voor nieuwere TLS-versies, zoals TLS 1.2. Als u wilt zien welke versies van TLS door uw browser worden ondersteund, gaat u naar de [site van Qualys SSL Labs](https://www.ssllabs.com/) en klikt u op Test your **browser**. U wordt aangeraden een upgrade uit te voeren naar de nieuwste versie van uw webbrowser en bij voorkeur alleen TLS 1.2 in te stellen.

**TLS 1.2 inschakelen via de browser**

- **Microsoft Edge en Internet Explorer (beide zijn ingesteld met behulp van Internet Explorer)**

    1. Open Internet Explorer selecteer **Extra**  >  **Internetopties**  >  **Geavanceerd.**
    2. Selecteer in **het** gebied Beveiliging **de optie TLS 1.2** gebruiken en selecteer vervolgens **OK.**
    3. Sluit alle browservensters en start de Internet Explorer.

- **Google Chrome**

    1. Open Google Chrome, typ *chrome://settings/* in de adresbalk en druk op **Enter.**
    2. Vouw geavanceerde **opties** uit, ga naar het **gebied Systeem** en selecteer **Proxy-instellingen openen.**
    3. Selecteer in het **vak Interneteigenschappen** het tabblad  **Geavanceerd,** ga naar het gebied Beveiliging, selecteer **TLS 1.2** gebruiken en selecteer **vervolgens OK.**
    4. Sluit alle browservensters en start Google Chrome opnieuw.

- **Mozilla Firefox**

    1. Open Firefox, typ *about:config in* de adresbalk en druk op **Enter.**
    2. Zoek de term *TLS* en selecteer vervolgens de **vermelding security.tls.version.max.**
    3. Stel de waarde in op **3** om af te dwingen dat de browser gebruik maakt van versie TLS 1.2 en selecteer **vervolgens OK.**

        >[!NOTE]
        >Firefox-versie 60.0 ondersteunt TLS 1.3, dus u kunt ook de waarde security.tls.version.max instellen op **4**.

    4. Sluit alle browservensters en start Mozilla Firefox opnieuw.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - juni 2018

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In juni 2018 hebben we deze 15 nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[Skytap](../saas-apps/skytap-tutorial.md), [Settling music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory. Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-wachtwoordbeveiliging is beschikbaar in de openbare preview

**Type:** Nieuwe **functieservicecategorie:** Identity **Protection-productmogelijkheid:** Gebruikersverificatie

Gebruik Azure AD-wachtwoordbeveiliging om gemakkelijk te raden wachtwoorden uit uw omgeving te elimineren. Als u deze wachtwoorden elimineert, kunt u het risico op aanvallen door een wachtwoordlak verminderen.

Azure AD-wachtwoordbeveiliging helpt u met name bij het volgende:

- De accounts van uw organisatie beveiligen in zowel Azure AD als Windows Server Active Directory (AD).
- Voorkomt dat uw gebruikers wachtwoorden gebruiken in een lijst met meer dan 500 van de meest gebruikte wachtwoorden en meer dan 1 miljoen vervangingsvariaties van deze wachtwoorden.
- Beheer Azure AD-wachtwoordbeveiliging vanaf één locatie in de Azure AD-portal, voor zowel Azure AD als on-premises Windows Server AD.

Zie Voor meer informatie over Azure AD-wachtwoordbeveiliging, [elimineert u slechte wachtwoorden in uw organisatie.](../authentication/concept-password-ban-bad.md)

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe beleidssjabloon voor voorwaardelijke toegang voor alle gasten die is gemaakt tijdens het maken van de gebruiksvoorwaarden

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

Tijdens het maken van uw gebruiksvoorwaarden wordt ook een nieuwe beleidssjabloon voor voorwaardelijke toegang gemaakt voor 'alle gasten' en 'alle apps'. Met deze nieuwe beleidssjabloon wordt de zojuist gemaakte toU toegepast, waarbij het proces voor het maken en afdwingen van gasten wordt gestreamd.

Zie voor meer informatie [Azure Active Directory Gebruiksrechtovereenkomst functie](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nieuwe aangepaste beleidssjabloon voor voorwaardelijke toegang die is gemaakt tijdens het maken van de gebruiksvoorwaarden

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Governance

Tijdens het maken van uw gebruiksvoorwaarden wordt ook een nieuwe aangepaste beleidssjabloon voor voorwaardelijke toegang gemaakt. Met deze nieuwe beleidssjabloon kunt u de gebruiksaanwijzing maken en vervolgens meteen naar de blade voor het maken van het beleid voor voorwaardelijke toegang gaan, zonder dat u handmatig door de portal hoeft te navigeren.

Zie voor meer informatie [Azure Active Directory Gebruiksrechtovereenkomst functie](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Nieuwe en uitgebreide richtlijnen voor het implementeren van Azure AD Multi-Factor Authentication

**Type:** Nieuwe **functieservicecategorie:** Andere **productmogelijkheid:** Identity Security & Protection

We hebben nieuwe stapsgewijs nieuwe richtlijnen uitgebracht voor het implementeren van Azure AD Multi-Factor Authentication (MFA) in uw organisatie.

Als u de MFA-implementatiehandleiding wilt bekijken, gaat u naar de opslagplaats [Identity Deployment Guides](./active-directory-deployment-plans.md) op GitHub. Als u feedback wilt geven over de implementatiehandleidingen, gebruikt u het [formulier Feedback over implementatieplannen.](https://aka.ms/deploymentplanfeedback) Als u vragen hebt over de implementatiehandleidingen, kunt u contact met ons opnemen [via IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Gedelegeerde Azure AD-app-beheerrollen zijn in openbare preview

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor Enterprise **Apps:** Access Control

Beheerders kunnen nu app-beheertaken delegeren zonder de rol van globale beheerder toe te wijzen. De nieuwe rollen en mogelijkheden zijn:

- **Nieuwe standaard Azure AD-beheerdersrollen:**

    - **Toepassingsbeheerder.** Biedt de mogelijkheid om alle aspecten van alle apps te beheren, waaronder registratie, instellingen voor eenmalige aanmelding, app-toewijzingen en licenties, app-proxyinstellingen en toestemming (met uitzondering van Azure AD-resources).

    - **Cloud Application Administrator.** Verleent alle mogelijkheden van de toepassingsbeheerder, met uitzondering van app-proxy, omdat deze geen on-premises toegang biedt.

    - **Toepassingsontwikkelaar.** Biedt de mogelijkheid om app-registraties te maken, zelfs als de optie Gebruikers **toestaan apps** te registreren is uitgeschakeld.

- **Eigendom (registratie per app instellen en app per onderneming, vergelijkbaar met het proces van groepseigendom:**

    - **Eigenaar van app-registratie.** Biedt de mogelijkheid om alle aspecten van app-registratie in eigendom te beheren, met inbegrip van het app-manifest en het toevoegen van extra eigenaren.

    - **Eigenaar van bedrijfsapp.** Biedt de mogelijkheid om veel aspecten van bedrijfsapps in eigendom te beheren, waaronder instellingen voor eenmalige aanmelding, app-toewijzingen en toestemming (met uitzondering van Azure AD-resources).

Zie Azure AD gedelegeerde toepassingsbeheerrollen in openbare preview voor meer informatie [over de openbare preview.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. Zie Beheerdersrollen toewijzen in Azure Active Directory voor meer informatie [over Azure Active Directory.](../roles/permissions-reference.md)

---

## <a name="may-2018"></a>Mei 2018

### <a name="expressroute-support-changes"></a>Ondersteuningswijzigingen voor ExpressRoute

**Type:** Servicecategorie **wijzigen plannen:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Platform

Software as a Service, zoals Azure Active Directory (Azure AD), werkt het beste door rechtstreeks via internet te gaan, zonder ExpressRoute of andere privé-VPN-tunnels. Daarom bieden we op 1 augustus **2018** geen ondersteuning meer voor ExpressRoute voor Azure AD-services met behulp van openbare Azure-peering en Azure-community's in Microsoft-peering. Alle services die door deze wijziging worden beïnvloed, merken mogelijk dat Azure AD-verkeer geleidelijk wordt verplaatst van ExpressRoute naar internet.

Terwijl we onze ondersteuning wijzigen, weten we ook dat er nog steeds situaties zijn waarin u mogelijk een speciale set circuits moet gebruiken voor uw verificatieverkeer. Daarom blijft Azure AD ondersteuning bieden voor IP-bereikbeperkingen per tenant met behulp van ExpressRoute en services die al op Microsoft-peering staan met de community 'Andere Office 365 Online-services'. Als uw services worden beïnvloed, maar u ExpressRoute nodig hebt, moet u het volgende doen:

- **Als u gebruik hebt gemaakt van openbare Azure-peering.** Ga naar Microsoft-peering en meld u aan voor de community Andere **Office 365 Online-services (12076:5100).** Zie het artikel Move a public peering to Microsoft peering (Een openbare peering verplaatsen naar [Microsoft-peering) voor meer](../../expressroute/how-to-move-peering.md) informatie over het verplaatsen van openbare Azure-peering naar Microsoft-peering.

- **Als u met Microsoft-peering werkt.** Meld u aan voor **de community Andere Office 365 Online-service (12076:5100).** Zie de sectie Ondersteuning voor [BGP-community's](../../expressroute/expressroute-routing.md#bgp) van het artikel Routeringsvereisten voor ExpressRoute voor meer informatie over routeringsvereisten.

Als u speciale circuits moet blijven gebruiken, moet u met uw Microsoft-accountteam praten over het verkrijgen van autorisatie voor het gebruik van de community Andere **Office 365 Online-service (12076:5100).** Het door MS Office beheerde beoordelingsbord controleert of u deze circuits nodig hebt en zorgt ervoor dat u begrijpt wat de technische gevolgen zijn van het bewaren ervan. Niet-geautoriseerde abonnementen die routefilters voor Office 365 proberen te maken, ontvangen een foutbericht.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph voor beheerscenario's voor tou

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productfunctie: Ontwikkelaarservaring**

We hebben een Microsoft Graph toegevoegd voor het beheer van azure AD-gebruiksvoorwaarden. U kunt de gebruiksvoorwaardenobject maken, bijwerken en verwijderen.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD-eindpunt met meerdere tenants toevoegen als id-provider in Azure AD B2C

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

Met behulp van aangepast beleid kunt u nu het algemene Azure AD-eindpunt toevoegen als een id-provider in Azure AD B2C. Hierdoor hebt u één toegangspunt voor alle Azure AD-gebruikers die zich aanmelden bij uw toepassingen. Zie voor meer informatie Azure Active Directory B2C: Gebruikers toestaan zich aan te melden bij een [Azure AD-id-provider](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)met meerdere tenants met behulp van aangepast beleid.

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Gebruik Interne URL's voor toegang tot apps vanaf elke locatie met onze Mijn apps-extensie voor aanmelden en de Azure AD-toepassingsproxy

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **productmogelijkheid:** SSO

Gebruikers hebben nu toegang tot toepassingen via interne URL's, zelfs buiten uw bedrijfsnetwerk, met behulp van de Mijn apps-extensie voor beveiligde aanmelding voor Azure AD. Dit werkt met elke toepassing die u hebt gepubliceerd met behulp van Azure AD toepassingsproxy, in elke browser waar ook de browserextensie Toegangsvenster geïnstalleerd. De functionaliteit voor URL-omleiding wordt automatisch ingeschakeld zodra een gebruiker zich aanmeldt bij de extensie. De extensie kan worden gedownload op [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)en [Firefox.](https://go.microsoft.com/fwlink/?linkid=866366)

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory: gegevens in Europa voor Europa-klanten

**Type:** Nieuwe **functieservicecategorie:** Andere **productmogelijkheid:** GoLocal

Klanten in Europa vereisen dat hun gegevens in Europa blijven en niet worden gerepliceerd buiten Europese datacenters om te voldoen aan de privacy- en Europese wetten. Dit [artikel](./active-directory-data-storage-eu.md) bevat de specifieke informatie over welke identiteitsgegevens worden opgeslagen in Europa en biedt ook details over informatie die wordt opgeslagen buiten Europese datacenters.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nieuwe integraties van SaaS-apps voor het inrichten van gebruikers - mei 2018

**Type:** Nieuwe **functieservicecategorie:** App Provisioning **Product capability:** 3rd Party Integration

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikersidentiteiten in SaaS-toepassingen zoals Dropbox, Salesforce, ServiceNow en meer automatiseren. Voor mei 2018 hebben we ondersteuning voor het inrichten van gebruikers toegevoegd voor de volgende toepassingen in de Azure AD-app-galerie:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Zie voor een lijst met alle toepassingen die ondersteuning bieden voor het inrichten van gebruikers in de Azure [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) AD-galerie.

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-toegangsbeoordelingen van groepen en app-toegang bieden nu terugkerende beoordelingen

**Type:** Nieuwe **functieServicecategorie: Productmogelijkheid** voor **toegangsbeoordelingen:** Governance

Toegangsbeoordeling van groepen en apps is nu algemeen beschikbaar als onderdeel van Azure AD Premium P2.  Beheerders kunnen toegangsbeoordelingen van groepslidmaatschap en toepassingstoewijzingen configureren om automatisch met regelmatige intervallen, zoals maandelijks of elk kwartaal, opnieuw te worden uitgevoerd.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-activiteitenlogboeken (aanmeldingen en controle) zijn nu beschikbaar via MS Graph

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Azure AD-activiteitenlogboeken, die aanmeldingen en auditlogboeken bevat, zijn nu beschikbaar via Microsoft Graph API. We hebben twee eindpunten beschikbaar gemaakt via de Microsoft Graph API voor toegang tot deze logboeken. Bekijk onze documenten [voor programmatische](../reports-monitoring/concept-reporting-api.md) toegang tot Rapportage-API's van Azure AD om aan de slag te gaan.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Verbeteringen in de B2B-inwisselingservaring en het verlaten van een organisatie

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C

**Just-In-Time-aflossing:** Zodra u een resource deelt met een gastgebruiker met behulp van de B2B-API, hoeft u geen speciale uitnodigingsmail te verzenden. In de meeste gevallen heeft de gastgebruiker toegang tot de resource en wordt deze net op tijd door de inwisselervaring genomen. Geen gevolgen meer vanwege gemiste e-mailberichten. U hoeft uw gastgebruikers niet meer te vragen: "Hebt u op die inwisselkoppeling geklikt die het systeem u heeft gestuurd?". Dit betekent dat wanneer SPO gebruikmaakt van de uitnodigingsmanager, cloudbijlagen dezelfde canonieke URL kunnen hebben voor alle gebruikers( intern en extern) in elke staat van inwisseling.

**Moderne inwisselervaring:** Startpagina voor inwisselen van gesplitst scherm niet meer. Gebruikers krijgen een moderne toestemmingservaring te zien met de privacyverklaring van de uitnodigende organisatie, net zoals bij apps van derden.

**Gastgebruikers kunnen de organisatie verlaten:** Zodra de relatie van een gebruiker met een organisatie is afgelopen, kan deze de organisatie zelf verlaten. Roep de beheerder van de uitnodigende organisatie niet meer aan om 'te worden verwijderd', geen ondersteuningstickets meer.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nieuwe federatief apps beschikbaar in Azure AD-app-galerie - mei 2018

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In mei 2018 hebben we deze 18 nieuwe apps met federatieondersteuning toegevoegd aan onze app-galerie:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), Montage [Online](../saas-apps/montageonline-tutorial.md), [まなびポケative](../saas-apps/manabipocket-tutorial.md), OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory.

Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nieuwe stapsgewijs implementatiehandleidingen voor Azure Active Directory

**Type:** Nieuwe **functieservicecategorie: Andere** **productmogelijkheid:** Directory

Nieuwe, stapsgewijze richtlijnen voor het implementeren van Azure Active Directory (Azure AD), waaronder selfservice voor wachtwoord opnieuw instellen (SSPR), eenmalige aanmelding (SSO), voorwaardelijke toegang, app-proxy, gebruikersinrichten, Active Directory Federation Services (ADFS) naar Pass-through-verificatie (PTA) en ADFS naar wachtwoordhashsynchronisatie (PHS).

Als u de implementatiehandleidingen wilt bekijken, gaat u naar de opslagplaats [Identity Deployment Guides](./active-directory-deployment-plans.md) op GitHub. Als u feedback wilt geven over de implementatiehandleidingen, gebruikt u het [formulier Feedback over implementatieplannen.](https://aka.ms/deploymentplanfeedback) Als u vragen hebt over de implementatiehandleidingen, kunt u contact met ons opnemen [via IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Zoeken in bedrijfstoepassingen - Meer apps laden

**Type:** Nieuwe **functieservicecategorie: Enterprise** Apps **Product capability:** SSO

Hebt u problemen met het vinden van uw toepassingen/service-principals? We hebben de mogelijkheid toegevoegd om meer toepassingen in de lijst met bedrijfstoepassingen te laden. Standaard worden 20 toepassingen weergegeven. U kunt nu klikken op **Meer laden om** aanvullende toepassingen te bekijken.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>De release van mei van AADConnect bevat een openbare preview van de integratie met PingFederate, belangrijke beveiligingsupdates, veel opgeloste fouten en nieuwe geweldige nieuwe hulpprogramma's voor probleemoplossing.

**Type:** **Functieservicecategorie gewijzigd:** AD **Connect-productmogelijkheid:** Identity Lifecycle Management

De release van mei van AADConnect bevat een openbare preview van de integratie met PingFederate, belangrijke beveiligingsupdates, veel opgeloste fouten en nieuwe geweldige nieuwe hulpprogramma's voor probleemoplossing. U vindt de opmerkingen bij de [release hier.](../hybrid/reference-connect-version-history.md)

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-toegangsbeoordelingen: automatisch toepassen

**Type:** **Functieservicecategorie gewijzigd: Productmogelijkheid** voor toegangsbeoordelingen: Governance 

Toegangsbeoordelingen van groepen en apps zijn nu algemeen beschikbaar als onderdeel van Azure AD Premium P2. Een beheerder kan configureren om de wijzigingen van de revisor automatisch toe te passen op die groep of app wanneer de toegangsbeoordeling is voltooid. De beheerder kan ook opgeven wat er gebeurt met de voortdurende toegang van de gebruiker als revisoren niet hebben gereageerd, de toegang hebben verwijderd, de toegang behouden of systeemaanbevelingen hebben gedaan.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Id-tokens kunnen niet meer worden geretourneerd met behulp van de query-response_mode voor nieuwe apps.

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Apps die zijn gemaakt op of na 25 april 2018 kunnen geen id_token meer aanvragen met behulp van de **response_mode.**   Hierdoor wordt Azure AD inline met de OIDC-specificaties en wordt de kans op aanvallen door uw apps verkleind.  Apps die zijn gemaakt vóór 25 april 2018, kunnen de **query-response_mode** niet gebruiken met een response_type van **id_token**.  De geretourneerde fout bij het aanvragen van een id_token van Azure AD is **AADSTS70007: 'query' is** geen ondersteunde waarde van 'response_mode' bij het aanvragen van een token .

Het **fragment** en **form_post** response_modes blijven werken: wanneer u nieuwe toepassingsobjecten maakt (bijvoorbeeld voor het gebruik van app-proxy), moet u ervoor zorgen dat een van deze response_modes wordt gebruikt voordat ze een nieuwe toepassing maken.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C toegangs token zijn ga

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U hebt nu toegang tot web-API's die zijn beveiligd door Azure AD B2C met behulp van toegangstokens. De functie wordt verplaatst van openbare preview naar algemeen beschikbaar. De gebruikersinterface voor het configureren Azure AD B2C toepassingen en web-API's is verbeterd en er zijn andere kleine verbeteringen aangebracht.

Zie voor meer informatie [Azure AD B2C: Toegangstokens aanvragen.](../../active-directory-b2c/access-tokens.md)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Een configuratie voor een aanmelding testen voor SAML-toepassingen

**Type:** Nieuwe **functieservicecategorie:** Enterprise Apps **Product capability:** SSO

Wanneer u op SAML gebaseerde SSO-toepassingen configureert, kunt u de integratie testen op de configuratiepagina. Als er een fout tijdens het aanmelden wordt weergegeven, kunt u de fout in de testervaring melden en biedt Azure AD u oplossingsstappen om het specifieke probleem op te lossen.

Zie voor meer informatie:

- [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](../manage-apps/view-applications-portal.md) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
- [Fouten opsporen in op SAML gebaseerde een aanmelding bij toepassingen in Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD-gebruiksvoorwaarden hebben nu rapportage per gebruiker

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

Beheerders kunnen nu een bepaalde toU selecteren en alle gebruikers zien die toestemming hebben gegeven voor die toU en welke datum/tijd deze heeft plaatsgevonden.

Zie de azure [AD-gebruiksvoorwaardenfunctie voor meer informatie.](../conditional-access/terms-of-use.md)

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riskant IP-adres voor AD FS extranetvergrendelingsbeveiliging

**Type:** Nieuwe **functieservicecategorie: Andere** **productmogelijkheid: Bewaking** van & rapportage

Connect Health ondersteunt nu de mogelijkheid om IP-adressen te detecteren die een drempelwaarde van mislukte U/P-aanmeldingen per uur of per dag overschrijden. De mogelijkheden van deze functie zijn:

- Uitgebreid rapport met IP-adressen en het aantal mislukte aanmeldingen dat per uur/dagelijks wordt gegenereerd met een aanpasbare drempelwaarde.
- Waarschuwingen op basis van e-mail die laten zien wanneer een specifiek IP-adres de drempelwaarde van mislukte U/P-aanmeldingen per uur/per dag heeft overschreden.
- Een downloadoptie om een gedetailleerde analyse van de gegevens uit te voeren

Zie Riskant IP-rapport [voor meer informatie.](../hybrid/how-to-connect-health-adfs.md)

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Eenvoudige app-configuratie met metagegevensbestand of URL

**Type:** Nieuwe **functieservicecategorie: Enterprise** Apps **Product capability:** SSO

Op de pagina Bedrijfstoepassingen kunnen beheerders een SAML-metagegevensbestand uploaden om een op SAML gebaseerde aanmelding te configureren voor de toepassing Azure AD Gallery en Non-Gallery.

Daarnaast kunt u de URL voor federatiemetagegevens van de Azure AD-toepassing gebruiken om eenmalige aanmelding te configureren met de doeltoepassing.

Zie [Configuring single sign-on to applications that](../manage-apps/view-applications-portal.md)are not in the Azure Active Directory application gallery voor meer informatie.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Gebruiksrechtovereenkomst nu algemeen beschikbaar

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving


De gebruiksvoorwaarden van Azure AD zijn verplaatst van openbare preview naar algemeen beschikbaar.

Zie de azure AD-gebruiksvoorwaardenfunctie [voor meer informatie.](../conditional-access/terms-of-use.md)

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen aan B2B-gebruikers van specifieke organisaties toestaan of blokkeren

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C


U kunt nu opgeven welke partnerorganisaties u wilt delen en samenwerken in Azure AD B2B Collaboration. Hiervoor kunt u ervoor kiezen om een lijst met specifieke domeinen voor toestaan of weigeren te maken. Wanneer een domein wordt geblokkeerd met behulp van deze mogelijkheden, kunnen werknemers geen uitnodigingen meer verzenden naar personen in dat domein.

Zo kunt u de toegang tot uw resources beheren en tegelijkertijd een soepele ervaring bieden voor goedgekeurde gebruikers.

Deze B2B Collaboration-functie is beschikbaar voor alle Azure Active Directory-klanten en kan worden gebruikt in combinatie met Azure AD Premium-functies zoals voorwaardelijke toegang en identiteitsbeveiliging voor een gedetailleerdere controle over wanneer en hoe externe zakelijke gebruikers zich aanmelden en toegang krijgen.

Zie Uitnodigingen voor [B2B-gebruikers](../external-identities/allow-deny-list.md)van specifieke organisaties toestaan of blokkeren voor meer informatie.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatief apps die beschikbaar zijn in de Azure AD-app-galerie

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In april 2018 hebben we deze 13 nieuwe apps met federatieondersteuning toegevoegd aan onze app-galerie:

Criterion HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now,](../saas-apps/orgchartnow-tutorial.md) [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), Cisco [Cloud,](../saas-apps/ciscocloud-tutorial.md)Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory.

Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen (openbare preview)

**Type:** Nieuwe **functieservicecategorie:** **B2B-productmogelijkheid:** B2B/B2C

Als organisatie die gebruikmaakt van B2B-samenwerkingsmogelijkheden van Azure Active Directory (Azure AD) om gastgebruikers van partnerorganisaties uit te nodigen voor uw Azure AD, kunt u deze B2B-gebruikers nu toegang bieden tot on-premises apps. Deze on-premises apps kunnen gebruikmaken van verificatie op basis van SAML of Geïntegreerde Windows-verificatie (IWA) met beperkte Kerberos-delegering (KCD).

Zie [B2B-gebruikers in Azure AD](../external-identities/hybrid-cloud-to-on-premises.md)toegang verlenen tot uw on-premises toepassingen voor meer informatie.

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Zelfstudies voor SSO-integratie van de Azure Marketplace

**Type:** **Functieservicecategorie gewijzigd: Andere** **productmogelijkheid:** Integratie van derden

Als een toepassing die wordt vermeld in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) een aanmelding op  basis van SAML ondersteunt, krijgt u de integratiezelfstudie die aan die toepassing is gekoppeld als u op Nu krijgen klikt.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snellere prestaties van het automatisch inrichten van gebruikers in Azure AD voor SaaS-toepassingen

**Type:** **Functieservicecategorie gewijzigd:** App Provisioning **Product capability:** 3rd Party Integration

Voorheen konden klanten die de connectors voor Azure Active Directory-gebruikers inrichten voor SaaS-toepassingen (bijvoorbeeld Salesforce, ServiceNow en Box) trage prestaties ervaren als hun Azure AD-tenants meer dan 100.000 gecombineerde gebruikers en groepen bevatten en ze gebruikers- en groepstoewijzingen gebruiken om te bepalen welke gebruikers moeten worden ingericht.

Op 2 april 2018 zijn er aanzienlijke prestatieverbeteringen geïmplementeerd in de Azure AD-inrichtingsservice, waardoor de initiële synchronisatie tussen Azure Active Directory- en doel-SaaS-toepassingen aanzienlijk minder lang nodig is.

Als gevolg hiervan worden veel klanten met initiële synchronisaties met apps die veel dagen duurden of nooit voltooid, nu binnen een paar minuten of uren voltooid.

Zie Wat gebeurt er tijdens [het inrichten? voor meer informatie.](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Selfservice voor wachtwoord opnieuw instellen vanaf Windows 10 vergrendelingsscherm voor hybride Azure AD-machines

**Type:** **Functieservicecategorie gewijzigd:** Selfservice voor het opnieuw instellen van het **wachtwoord Product:** Gebruikersverificatie

We hebben de functie Windows 10 SSPR bijgewerkt met ondersteuning voor machines die zijn toegevoegd aan hybride Azure AD. Deze functie is beschikbaar in Windows 10 RS4 stelt gebruikers in staat hun wachtwoord opnieuw in te stellen vanaf het vergrendelingsscherm van een Windows 10 machine. Gebruikers die zijn ingeschakeld en geregistreerd voor selfservice voor wachtwoord opnieuw instellen, kunnen deze functie gebruiken.

Zie Azure AD-wachtwoord opnieuw instellen vanuit [het aanmeldingsscherm voor meer informatie.](../authentication/howto-sspr-windows.md)

---

## <a name="march-2018"></a>Maart 2018

### <a name="certificate-expire-notification"></a>Melding over verlopen van certificaat

**Type:** Vaste **servicecategorie:** Enterprise Apps **Product capability:** SSO

Azure AD verzendt een melding wanneer een certificaat voor een galerie- of niet-galerietoepassing bijna verloopt.

Sommige gebruikers hebben geen meldingen ontvangen voor bedrijfstoepassingen die zijn geconfigureerd voor een aanmelding op basis van SAML. Dit probleem is opgelost. Azure AD verzendt een melding voor certificaten die binnen 7, 30 en 60 dagen verlopen. U kunt deze gebeurtenis zien in de auditlogboeken.

Zie voor meer informatie:

- [Certificaten beheren voor federatief een aanmelding in Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Controleactiviteitenrapporten in Azure Active Directory Portal](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Id-providers van Twitter en GitHub in Azure AD B2C

**Type:** Nieuwe **functieservicecategorie:** B2C - Consumer Identity Management **Product capability:** B2B/B2C

U kunt nu Twitter of GitHub toevoegen als id-provider in Azure AD B2C. Twitter verhuist van openbare preview naar GA. GitHub wordt uitgebracht in openbare preview.

Zie Wat [is Azure AD B2B-samenwerking? voor meer informatie.](../external-identities/what-is-b2b.md)

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Browsertoegang beperken met Intune Managed Browser met voorwaardelijke toegang op basis van een Azure AD-toepassing voor iOS en Android

**Type:** Nieuwe **functieservicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identity Security & Protection

**Nu in openbare preview!**

**Intune Managed Browser SSO:** Uw werknemers kunnen een aanmelding gebruiken voor verschillende native clients (zoals Microsoft Outlook) en de Intune Managed Browser voor alle met Azure AD verbonden apps.

**Intune Managed Browser voor voorwaardelijke toegang:** U kunt werknemers nu verplichten om de door Intune beheerde browser te gebruiken met op toepassingen gebaseerd beleid voor voorwaardelijke toegang.

Lees meer over dit in onze [blogpost](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Zie voor meer informatie:

- [Voorwaardelijke toegang op basis van een toepassing instellen](../conditional-access/app-based-conditional-access.md)

- [Beheerde-browserbeleid configureren](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App Proxy-cmdlets in PowerShell GA-module

**Type:** Nieuwe **functieservicecategorie: App** **Proxy-productmogelijkheid:** Access Control

Ondersteuning voor toepassingsproxy-cmdlets is nu in de PowerShell GA-module. Hiervoor moet u op de hoogte blijven van PowerShell-modules. Als u meer dan een jaar achter bent, werken sommige cmdlets mogelijk niet meer.

Zie AzureAD voor [meer informatie.](/powershell/module/Azuread/)

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 native clients worden ondersteund door naadloze eenmalige aanmelding met behulp van een niet-interactief protocol

**Type:** Nieuwe **functieServicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Gebruikers die gebruikmaken van native Office 365-clients (versie 16.0.8730.xxxx en hoger) krijgen een stille aanmeldingservaring met naadloze eenmalige aanmelding. Deze ondersteuning wordt geboden door de toevoeging van een niet-interactief protocol (WS-Trust) aan Azure AD.

Zie Hoe werkt aanmelding op een native client met [naadloze eenmalige aanmelding?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work) voor meer informatie.

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Gebruikers krijgen een stille aanmeldingservaring, met naadloze eenmalige aanmelding, als een toepassing aanmeldingsaanvragen verzendt naar de tenant-eindpunten van Azure AD

**Type:** Nieuwe **functieServicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Gebruikers krijgen een stille aanmeldingservaring, met naadloze eenmalige aanmelding, als een toepassing (bijvoorbeeld ) aanmeldingsaanvragen verzendt naar de tenant-eindpunten van Azure AD , dat wil zeggen, of - in plaats van het algemene eindpunt van `https://contoso.sharepoint.com` `https://login.microsoftonline.com/contoso.com/<..>` Azure AD ( `https://login.microsoftonline.com/<tenant_ID>/<..>` `https://login.microsoftonline.com/common/<...>` ).

Zie naadloze een Azure Active Directory [voor meer informatie.](../hybrid/how-to-connect-sso.md)

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>U hoeft voorheen slechts één Azure AD-URL in plaats van twee URL's toe te voegen aan de intranetzone-instellingen van gebruikers om naadloze eenmalige aanmelding uit te rollen

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Als u naadloze eenmalige aanmelding voor uw gebruikers wilt implementeren, hoeft u slechts één Azure AD-URL toe te voegen aan de intranetzone-instellingen van de gebruikers met behulp van groepsbeleid in Active Directory: `https://autologon.microsoftazuread-sso.com` . Voorheen moesten klanten twee URL's toevoegen.

Zie naadloze een Azure Active Directory [voor meer informatie.](../hybrid/how-to-connect-sso.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatief apps die beschikbaar zijn in de Azure AD-app-galerie

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In maart 2018 hebben we deze 15 nieuwe apps met federatieondersteuning toegevoegd aan onze app-galerie:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant van FirstCloud, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory.

Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het [in](../develop/v2-howto-app-gallery-listing.md)de azure AD-appgalerie.

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM voor Azure-resources is algemeen beschikbaar

**Type:** Nieuwe **functieservicecategorie: Privileged Identity Management** **productmogelijkheid:** Privileged Identity Management

Als u Azure AD Privileged Identity Management gebruikt voor adreslijstrollen, kunt u nu de tijdsgebonden toegangs- en toewijzingsmogelijkheden van PIM gebruiken voor Azure-resourcerollen, zoals abonnementen, resourcegroepen, Virtual Machines en andere resources die worden ondersteund door Azure Resource Manager. Multi-Factor Authentication afdwingen bij het activeren van functies Just-In-Time en het plannen van activeringen in coördinatie met goedgekeurde wijzigingsvensters. Daarnaast voegt deze release verbeteringen toe die niet beschikbaar zijn tijdens de openbare preview, waaronder een bijgewerkte gebruikersinterface, goedkeuringswerkstromen en de mogelijkheid om binnenkort verlopen rollen uit te breiden en verlopen rollen te vernieuwen.

Zie PIM voor [Azure-resources (preview) voor meer informatie](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Optionele claims toevoegen aan uw apps-tokens (openbare preview)

**Type:** Nieuwe **functieServicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Uw Azure AD-app kan nu aangepaste of optionele claims aanvragen in JWT's of SAML-tokens.  Dit zijn claims over de gebruiker of tenant die niet standaard zijn opgenomen in het token, vanwege grootte- of toepasbaarheidsbeperkingen.  Dit is momenteel in openbare preview voor Azure AD-apps op de v1.0- en v2.0-eindpunten.  Zie de documentatie voor informatie over welke claims kunnen worden toegevoegd en hoe u uw toepassingsmanifest bewerkt om deze aan te vragen.

Zie Optionele [claims in Azure AD voor meer informatie.](../develop/active-directory-optional-claims.md)

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD ondersteunt PKCE voor veiligere OAuth-stromen

**Type:** Nieuwe **functieServicecategorie:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Azure AD-documenten zijn bijgewerkt met ondersteuning voor PKCE, waardoor veiligere communicatie mogelijk is tijdens de goedkeuringsstroom voor OAuth 2.0-autorisatiecode.  Zowel S256- als code_challenges worden ondersteund op de v1.0- en v2.0-eindpunten.

Zie Een autorisatiecode [aanvragen voor meer informatie.](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Ondersteuning voor het inrichten van alle gebruikerskenmerkwaarden die beschikbaar zijn in de Workday Get_Workers-API

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor **app-inrichting:** Integratie van derden

De openbare preview van binnenkomende inrichting van Workday naar Active Directory en Azure AD ondersteunt nu de mogelijkheid om alle kenmerkwaarden die beschikbaar zijn in de Workday Get_Workers-API te extraheren en in terichten. Hiermee worden honderden extra standaard- en aangepaste kenmerken toegevoegd naast de kenmerken die worden geleverd met de eerste versie van de Inkomende inrichtingsconnector van Workday.

Zie voor meer informatie: [De lijst met Workday-gebruikerskenmerken aanpassen](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Groepslidmaatschap wijzigen van dynamisch in statisch, en vice versa

**Type:** Nieuwe **functieservicecategorie:** Mogelijkheid van **groepsbeheerproduct:** Samenwerking

Het is mogelijk om te wijzigen hoe lidmaatschap wordt beheerd in een groep. Dit is handig als u dezelfde groepsnaam en id in het systeem wilt houden, zodat bestaande verwijzingen naar de groep nog steeds geldig zijn; Voor het maken van een nieuwe groep moeten deze verwijzingen worden bijgewerkt.
We hebben het Azure AD-beheercentrum bijgewerkt om deze functionaliteit te ondersteunen. Klanten kunnen nu bestaande groepen converteren van dynamisch lidmaatschap naar toegewezen lidmaatschap en vice versa. De bestaande PowerShell-cmdlets zijn ook nog steeds beschikbaar.

Zie Dynamische lidmaatschapsregels [voor groepen in Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Verbeterd aanmeldingsgedrag met naadloze eenmalige aanmelding

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Voorheen zouden gebruikers, zelfs als ze zich expliciet zouden hebben aangemeld bij een toepassing die wordt beveiligd door Azure AD, automatisch opnieuw worden aangemeld met naadloze eenmalige aanmelding als ze opnieuw toegang proberen te krijgen tot een Azure AD-toepassing in hun corpnet vanaf hun apparaten die lid zijn van het domein. Met deze wijziging wordt de uitloggen ondersteund.  Hierdoor kunnen gebruikers hetzelfde of een ander Azure AD-account kiezen om zich opnieuw bij aan te melden, in plaats van dat ze automatisch worden aangemeld met naadloze eenmalige aanmelding.

Zie Naadloze een Azure Active Directory [voor meer informatie](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>toepassingsproxy connectorversie 1.5.402.0 uitgebracht

**Type:** **Functieservicecategorie gewijzigd:** App Proxy **Product capability:** Identity Security & Protection

Deze connectorversie wordt geleidelijk tot en met november uitgerold. Deze nieuwe connectorversie bevat de volgende wijzigingen:

- De connector stelt nu cookies op domeinniveau in plaats van subdomeinniveau in. Dit zorgt voor een soepelere SSO-ervaring en voorkomt redundante verificatieprompts.
- Ondersteuning voor gese chunkding-aanvragen
- Verbeterde statuscontrole van connectors
- Verschillende opgeloste fouten en stabiliteitsverbeteringen

Zie Understand [Azure AD toepassingsproxy connectors (Azure AD toepassingsproxy-connectors) voor meer informatie.](../manage-apps/application-proxy-connectors.md)

---

## <a name="february-2018"></a>Februari 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Verbeterde navigatie voor het beheren van gebruikers en groepen

**Type:** Servicecategorie **wijzigen plannen:** Directory **Management-productmogelijkheid:** Directory

De navigatie-ervaring voor het beheren van gebruikers en groepen is gestroomlijnd. U kunt nu rechtstreeks vanuit het directoryoverzicht naar de lijst met alle gebruikers navigeren, met eenvoudigere toegang tot de lijst met verwijderde gebruikers. U kunt ook rechtstreeks vanuit het directoryoverzicht naar de lijst met alle groepen navigeren, met eenvoudigere toegang tot instellingen voor groepsbeheer. En ook op de overzichtspagina van de directory kunt u zoeken naar een gebruiker, groep, bedrijfstoepassing of app-registratie.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Beschikbaarheid van aanmeldingen en controlerapporten in Microsoft Azure beheerd door 21Vianet (Azure China 21Vianet)

**Type:** Nieuwe **functieservicecategorie: Azure Stack** **productfunctie: Bewaking** van & rapportage

Azure AD-activiteitenlogboekrapporten zijn nu beschikbaar in Microsoft Azure beheerd door 21Vianet-exemplaren (Azure China 21Vianet). De volgende logboeken zijn opgenomen:

- **Activiteitenlogboeken voor aanmeldingen:**  bevat alle aanmeldingslogboeken die zijn gekoppeld aan uw tenant.

- **Selfservice voor wachtwoordcontrolelogboeken:** bevat alle SSPR-auditlogboeken.

- **Auditlogboeken voor** adreslijstbeheer: bevat alle auditlogboeken met betrekking tot adreslijstbeheer, zoals gebruikersbeheer, app-beheer en andere.

Met deze logboeken kunt u inzicht krijgen in de manier waarop uw omgeving het doet. Met de gegevens kunt u het volgende doen:

- Bepaal hoe uw apps en services worden gebruikt door uw gebruikers.

- Problemen oplossen die verhinderen dat uw gebruikers hun werk kunnen doen.

Zie voor meer informatie over het gebruik van deze rapporten [Azure Active Directory rapportage.](../reports-monitoring/overview-reports.md)

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>De rol Rapportlezer (niet-beheerdersrol) gebruiken om Azure AD-activiteitenrapporten weer te geven

**Type:** Nieuwe **functieservicecategorie:** **Rapportageproductmogelijkheid: Bewaking** van & rapportage

Als onderdeel van feedback van klanten om niet-beheerdersrollen toegang te geven tot Activiteitenlogboeken van Azure AD, hebben we de mogelijkheid ingeschakeld voor gebruikers met de rol 'Rapportlezer' voor toegang tot aanmeldingen en controleactiviteiten binnen de Azure Portal en voor het gebruik van de Microsoft Graph-API.

Zie voor meer informatie over het gebruik van deze rapporten [Azure Active Directory rapportage.](../reports-monitoring/overview-reports.md)

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-claim beschikbaar als gebruikerskenmerk en gebruikers-id

**Type:** Nieuwe **functieservicecategorie:** Enterprise Apps **Product capability:** SSO

U kunt **EmployeeID configureren** als gebruikers-id en gebruikerskenmerk voor lidgebruikers en B2B-gasten in op SAML gebaseerde aanmeldingstoepassingen vanuit de gebruikersinterface van de Enterprise-toepassing.

Zie Claims aanpassen die zijn [uitgegeven in het SAML-token voor bedrijfstoepassingen in Azure Active Directory.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Vereenvoudigd toepassingsbeheer met jokertekens in Azure AD toepassingsproxy

**Type:** Nieuwe **functieservicecategorie:** App **Proxy-productmogelijkheid:** Gebruikersverificatie

Om de implementatie van toepassingen eenvoudiger te maken en uw administratieve overhead te verminderen, ondersteunen we nu de mogelijkheid om toepassingen te publiceren met jokertekens. Als u een toepassing met jokertekens wilt publiceren, kunt u de standaardstroom voor het publiceren van toepassingen volgen, maar een jokerteken gebruiken in de interne en externe URL's.

Zie Toepassingen met [jokertekens in de Azure Active Directory toepassingsproxy voor meer informatie](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nieuwe cmdlets ter ondersteuning van de configuratie van toepassingsproxy

**Type:** Nieuwe **functieservicecategorie:** App **Proxy-productmogelijkheid:** Platform

De nieuwste versie van de AzureAD PowerShell Preview-module bevat nieuwe cmdlets waarmee klanten toepassingen kunnen toepassingsproxy configureren met behulp van PowerShell.

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

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nieuwe cmdlets ter ondersteuning van de configuratie van groepen

**Type:** Nieuwe **functieservicecategorie:** App **Proxy-productmogelijkheid:** Platform

De nieuwste versie van de AzureAD PowerShell-module bevat cmdlets voor het beheren van groepen in Azure AD. Deze cmdlets waren eerder beschikbaar in de AzureADPreview-module en zijn nu toegevoegd aan de AzureAD-module

De Groep-cmdlets die nu beschikbaar zijn voor algemene beschikbaarheid zijn:

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

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Er is een nieuwe Azure AD Connect beschikbaar

**Type:** Nieuwe **functieservicecategorie: AD Sync** **productmogelijkheid:** Platform

Azure AD Connect is het voorkeurshulpprogramma voor het synchroniseren van gegevens tussen Azure AD en on-premises gegevensbronnen, waaronder Windows Server Active Directory en LDAP.

>[!Important]
>Deze build introduceert wijzigingen in schema- en synchronisatieregel. De Azure AD Connect-synchronisatieservice activeert een volledige import- en volledige synchronisatiestappen na een upgrade. Zie Volledige synchronisatie uitstellen na de upgrade voor meer informatie over het wijzigen [van dit gedrag.](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)

Deze release heeft de volgende updates en wijzigingen:

**Problemen opgelost**

- Het tijdvenster voor achtergrondtaken voor de pagina Partitiefiltering is opgelost bij het overschakelen naar de volgende pagina.

- Er is een fout opgelost die de toegangsschending heeft veroorzaakt tijdens de aangepaste ConfigDB-actie.

- Er is een fout opgelost om te herstellen na een time-out van de SQL-verbinding.

- Er is een fout opgelost waarbij certificaten met SAN-jokertekens niet kunnen worden vóór de req-controle.

- Er is een fout opgelost die ervoor zorgt miiserver.exe tijdens het exporteren van de Azure AD-connector.

- Er is een fout opgelost waarbij een mislukte wachtwoordpoging op dc is aangemeld bij het uitvoeren van, waardoor de Azure AD Connect-wizard de configuratie heeft gewijzigd

**Nieuwe functies en verbeteringen**

- Telemetrie van toepassingen: beheerders kunnen deze gegevensklasse in-/uitschakelen.

- Azure AD Health-gegevens: beheerders moeten de statusportal bezoeken om hun statusinstellingen te beheren. Zodra het servicebeleid is gewijzigd, worden deze door de agents gelezen en afgedwongen.

- Configuratieacties voor write-back van apparaat en een voortgangsbalk toegevoegd voor pagina-initialisatie.

- Algemene diagnostische gegevens verbeterd met HTML-rapport en volledige gegevensverzameling in een ZIP-Text/HTML-rapport.

- Verbeterde betrouwbaarheid van automatische upgrade en extra telemetrie toegevoegd om ervoor te zorgen dat de status van de server kan worden bepaald.

- Machtigingen beperken die beschikbaar zijn voor bevoegde accounts in een AD Connector-account. Voor nieuwe installaties beperkt de wizard de machtigingen die bevoegde accounts hebben voor het MSOL-account na het maken van het MSOL-account. De wijzigingen zijn van invloed op expresinstallaties en aangepaste installaties met een account voor automatisch maken.

- Het installatieprogramma is gewijzigd om geen SA-bevoegdheid te vereisen bij een schone installatie van AADConnect.

- Nieuw hulpprogramma voor het oplossen van synchronisatieproblemen voor een specifiek object. Op dit moment controleert het hulpprogramma op de volgende zaken:

    - UserPrincipalName komt niet overeen tussen het gesynchroniseerde gebruikersobject en het gebruikersaccount in de Azure AD-tenant.

    - Als het object is gefilterd op synchronisatie vanwege domeinfiltering

    - Als het object wordt gefilterd op synchronisatie vanwege filteren van organisatie-eenheid (OE)

- Nieuw hulpprogramma voor het synchroniseren van de huidige wachtwoordhash die is opgeslagen in de on-premises Active Directory voor een specifiek gebruikersaccount. Het hulpprogramma vereist geen wachtwoordwijziging.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Toepassingen die ondersteuning Intune-app-beveiliging toegevoegd voor gebruik met voorwaardelijke toegang op basis van azure AD-toepassingen

**Type:** **Functieservicecategorie gewijzigd: Voorwaardelijke** **toegang Productmogelijkheid:** Identity Security & Protection

We hebben meer toepassingen toegevoegd die voorwaardelijke toegang op basis van toepassingen ondersteunen. U kunt nu toegang krijgen tot Office 365 en andere met Azure AD verbonden cloud-apps met behulp van deze goedgekeurde client-apps.

De volgende toepassingen worden aan het einde van februari toegevoegd:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Zie voor meer informatie:

- [Vereiste voor goedgekeurde client-app](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Gebruiksrechtovereenkomst bijwerken naar mobiele ervaring

**Type:** **Functieservicecategorie gewijzigd: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

Wanneer de gebruiksvoorwaarden worden weergegeven, kunt u nu klikken op **Problemen met weergeven? Klik hier.** Als u op deze koppeling klikt, worden de systeemeigen gebruiksvoorwaarden op uw apparaat geopend. Ongeacht de tekengrootte in het document of de schermgrootte van het apparaat, kunt u zo nodig inzoomen en het document lezen.

---

## <a name="january-2018"></a>Januari 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nieuwe federatief apps die beschikbaar zijn in de Azure AD-app-galerie

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor Enterprise **Apps:** Integratie van derden

In januari 2018 zijn de volgende nieuwe apps met federatieondersteuning toegevoegd aan de app-galerie:

[IBM OpenPages,](../saas-apps/ibmopenpages-tutorial.md) [OneTrust Privacy Management Software,](../saas-apps/onetrust-tutorial.md) [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk Federated Directory en [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Zie Integratie van [SaaS-toepassingen met](../saas-apps/tutorial-list.md)Azure Active Directory voor meer Azure Active Directory.

Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="sign-in-with-additional-risk-detected"></a>Aanmelden met extra risico gedetecteerd

**Type:** Nieuwe **functieServicecategorie:** Identity Protection **Product capability:** Identity Security & Protection

Het inzicht dat u krijgt voor een gedetecteerde risicodetectie is gekoppeld aan uw Azure AD-abonnement. Met de Azure AD Premium P2-editie krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

Met de Azure AD Premium P1-editie worden detecties die niet onder uw licentie vallen, weergegeven als de aanmelding voor risicodetectie met extra risico gedetecteerd.

Zie risicodetecties voor [Azure Active Directory meer informatie.](../identity-protection/overview-identity-protection.md)

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-toepassingen verbergen voor toegangspaneel van eindgebruikers

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **productfunctie:** SSO

U kunt nu beter beheren hoe Office 365-toepassingen worden weer geven op de toegangspaneel van uw gebruiker via een nieuwe gebruikersinstelling. Deze optie is handig voor het verminderen van het aantal apps in de toegangspaneel van een gebruiker als u liever alleen Office-apps in de Office-portal wilt tonen. De instelling bevindt zich in de **gebruikersinstellingen** en heeft het label Gebruikers kunnen alleen **Office 365-apps zien in de Office 365-portal.**

Zie Een toepassing verbergen [voor de gebruikerservaring in](../manage-apps/hide-application-from-user-portal.md)Azure Active Directory voor meer Azure Active Directory.

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Naadloze aanmelding bij apps die zijn ingeschakeld voor wachtwoord-SSO rechtstreeks vanuit de URL van de app

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **productfunctie:** SSO

De Mijn apps-browseruitbreiding is nu beschikbaar via een handig hulpprogramma waarmee u de Mijn apps mogelijkheid voor een aanmelding als snelkoppeling in uw browser kunt gebruiken. Na de installatie zien gebruikers een wafelpictogram in hun browser, zodat ze snel toegang hebben tot apps. Gebruikers kunnen nu profiteren van:

- De mogelijkheid om u rechtstreeks aan te melden bij apps op basis van wachtwoord-SSO vanaf de aanmeldingspagina van de app
- Een app starten met behulp van de functie voor snel zoeken
- Snelkoppelingen naar recent gebruikte apps vanuit de extensie
- De extensie is beschikbaar voor Microsoft Edge, Chrome en Firefox.

Zie Beveiligde aanmeldingsextensie [Mijn apps meer informatie.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-beheerervaring in de klassieke Azure-portal is niet meer beschikbaar

**Type:** Afgeschafte **servicecategorie: Azure** **AD-productmogelijkheid:** Directory

Vanaf 8 januari 2018 is de Azure AD-beheerervaring in de klassieke Azure-portal gestopt. Dit heeft plaatsgevonden in combinatie met de pensioen van de klassieke Azure-portal zelf. In de toekomst moet u het [Azure AD-beheercentrum gebruiken](https://aad.portal.azure.com) voor al uw portalbeheer van Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>De PhoneFactor-webportal is niet meer gebruikt

**Type:** Afgeschafte **servicecategorie: Azure** **AD-productmogelijkheid:** Directory

Vanaf 8 januari 2018 is de PhoneFactor-webportal uit gebruik genomen. Deze portal is gebruikt voor het beheer van de MFA-server, maar deze functies zijn verplaatst naar Azure Portal portal.azure.com.

De MFA-configuratie bevindt zich op: **Azure Active Directory \> MFA-server**

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afgeschaft

**Type:** Afgeschafte **servicecategorie:** Rapportageproductmogelijkheid: Identiteitslevenscyclusbeheer 


Nu de nieuwe Azure Active Directory Administration-console en nieuwe API's algemeen beschikbaar zijn voor zowel activiteiten- als beveiligingsrapporten, zijn de rapport-API's onder het eindpunt '/reports' vanaf eind 31 december 2017 uit gebruik genomen.

**Wat is er beschikbaar?**

Als onderdeel van de overgang naar de nieuwe beheerconsole hebben we twee nieuwe API's beschikbaar gemaakt voor het ophalen van Azure AD-activiteitenlogboeken. De nieuwe set API's biedt uitgebreidere filter- en sorteerfunctionaliteit, naast uitgebreidere controle- en aanmeldingsactiviteiten. De gegevens die eerder beschikbaar waren via de beveiligingsrapporten, zijn nu toegankelijk via de API voor risicodetectie van Identiteitsbeveiliging in Microsoft Graph.

Zie voor meer informatie:

- [Aan de slag met Azure Active Directory rapportage-API](../reports-monitoring/concept-reporting-api.md)

- [Aan de slag Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Gebruiksrechtovereenkomst in de Toegangsvenster

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

U kunt nu naar de Toegangsvenster en de gebruiksvoorwaarden bekijken die u eerder hebt geaccepteerd.

Volg deze stappen:

1. Ga naar de [MyApps-portal](https://myapps.microsoft.com)en meld u aan.

2. Selecteer uw naam in de rechterbovenhoek en selecteer vervolgens **Profiel** in de lijst.

3. Selecteer in **uw profiel** **De gebruiksvoorwaarden controleren.**

4. U kunt nu de gebruiksvoorwaarden bekijken die u hebt geaccepteerd.

Zie de azure AD-gebruiksvoorwaardenfunctie [(preview) voor meer informatie.](../conditional-access/terms-of-use.md)

---

### <a name="new-azure-ad-sign-in-experience"></a>Nieuwe azure AD-aanmeldingservaring

**Type:** Nieuwe **functieservicecategorie:** Azure **AD-productmogelijkheid:** Gebruikersverificatie

De Azure AD- en Microsoft-account's van het identiteitssysteem zijn opnieuw ontworpen, zodat ze een consistent uiterlijk hebben. Daarnaast wordt op de aanmeldingspagina van Azure AD eerst de gebruikersnaam verzameld, gevolgd door de referentie op een tweede scherm.

Zie De nieuwe Azure [AD-aanmeldingservaring is nu beschikbaar als openbare preview voor meer informatie.](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Minder aanmeldingsprompts: een nieuwe ervaring voor aanmelden bij Azure AD

**Type:** Nieuwe **functieservicecategorie:** Azure **AD-productmogelijkheid:** Gebruikersverificatie

Het **selectievakje Aangemeld blijven op** de azure AD-aanmeldingspagina is vervangen door een nieuwe prompt die wordt weergegeven nadat u zich hebt geverifieerd.

Als u ja **op deze** prompt reageert, geeft de service u een permanent vernieuwingsteken. Dit gedrag is hetzelfde als toen u het selectievakje Aangemeld blijven **in** de oude ervaring hebt ingeschakeld. Voor federatief tenants wordt deze prompt weer te zien nadat u zich hebt geverifieerd bij de federatief-service.

Zie Minder aanmeldingsprompts: de nieuwe ervaring 'aangemeld blijven' voor [Azure AD is in preview voor meer informatie.](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Configuratie toevoegen om te vereisen dat de gebruiksvoorwaarden worden uitgebreid voordat u akkoord gaat

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

Een optie voor beheerders vereist dat hun gebruikers de gebruiksvoorwaarden uitbreiden voordat ze de voorwaarden accepteren.

Selecteer Aan **of** **Uit om** te vereisen dat gebruikers de gebruiksvoorwaarden uitbreiden. Bij **de** instelling Aan moeten gebruikers de gebruiksvoorwaarden bekijken voordat ze deze accepteren.

Zie de azure AD-gebruiksvoorwaardenfunctie [(preview) voor meer informatie.](../conditional-access/terms-of-use.md)

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Scoped activering voor in aanmerking komende roltoewijzingen

**Type:** Nieuwe **functieservicecategorie: Privileged Identity Management** **productmogelijkheid:** Privileged Identity Management

U kunt activering binnen bereik gebruiken om in aanmerking komende Toewijzingen van Azure-resourcerol te activeren met minder autonomie dan de oorspronkelijke toewijzingsinstellingen. Een voorbeeld hiervan is als u bent toegewezen als de eigenaar van een abonnement in uw tenant. Met activering binnen het bereik kunt u de rol van eigenaar activeren voor maximaal vijf resources in het abonnement (zoals resourcegroepen en virtuele machines). Het bereik van uw activering vermindert mogelijk de kans op het uitvoeren van ongewenste wijzigingen in kritieke Azure-resources.

Zie Wat [is Azure AD Privileged Identity Management? voor meer informatie.](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nieuwe federatief apps in de Azure AD-app-galerie

**Type:** Nieuwe **functieservicecategorie: Productmogelijkheid** voor **bedrijfsapps:** Integratie van derden

In december 2017 hebben we deze nieuwe apps met federatieondersteuning toegevoegd aan onze app-galerie:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD-integratie](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO voor Bamboo van resolution GmbH,](../saas-apps/bamboo-tutorial.md) [SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD Integration.

Zie Integratie van SaaS-toepassingen met Azure Active Directory voor meer informatie [over de apps.](../saas-apps/tutorial-list.md)

Zie Uw toepassing in de galerie met toepassingen Azure Active Directory voor meer informatie over het in de galerie met Azure [AD-apps.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Goedkeuringswerkstromen voor Azure AD-directoryrollen

**Type:** **Functieservicecategorie gewijzigd: Privileged Identity Management** **productfunctie:** Privileged Identity Management

Goedkeuringswerkstroom voor Azure AD-directoryrollen is algemeen beschikbaar.

Met een goedkeuringswerkstroom kunnen beheerders met bevoorrechte rollen vereisen dat in aanmerking komende rolleden een rolactivering aanvragen voordat ze de bevoorrechte rol kunnen gebruiken. Er kunnen goedkeuringsverantwoordelijkheden worden gedelegeerd aan meerdere gebruikers en groepen. In aanmerking komende rolleden ontvangen meldingen wanneer de goedkeuring is voltooid en hun rol actief is.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Pass-through-verificatie: ondersteuning voor Skype voor Bedrijven

**Type:** **Functieservicecategorie gewijzigd:** Mogelijkheid voor verificaties (aanmeldingen) **Product:** Gebruikersverificatie

Pass-through-verificatie ondersteunt nu aanmeldingen van gebruikers bij Skype voor Bedrijven-clienttoepassingen die moderne verificatie ondersteunen, waaronder online- en hybride topologies.

Zie Skype voor [Bedrijven-topologies supported with modern authentication (Skype voor Bedrijven-topologies die worden ondersteund met moderne verificatie) voor meer informatie.](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Updates voor Azure AD Privileged Identity Management voor Azure RBAC (preview)

**Type:** **Functieservicecategorie gewijzigd: Privileged Identity Management** **productfunctie:** Privileged Identity Management

Met de openbare preview-versie van Azure AD Privileged Identity Management (PIM) voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u nu het volgende doen:

* Gebruik Just Enough Administration.
* Goedkeuring vereisen om resourcerollen te activeren.
* Plan een toekomstige activering van een rol die goedkeuring vereist voor zowel Azure AD- als Azure-rollen.

Zie Azure-resources [Privileged Identity Management (preview) voor meer informatie.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Access Control service stoppen

**Type:** Servicecategorie **wijzigen plannen:** productmogelijkheid Access Control **service:** Access Control service

Azure Active Directory Access Control (ook wel de Access Control service genoemd) wordt eind 2018 in gebruik genomen. Meer informatie met een gedetailleerd schema en richtlijnen voor migratie op hoog niveau vindt u in de komende weken. U kunt opmerkingen op deze pagina plaatsen met eventuele vragen over de Access Control service en een teamlid beantwoordt deze.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Browsertoegang tot de Intune Managed Browser

**Type:** Servicecategorie **wijzigen plannen: Voorwaardelijke** toegang **Productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

U kunt browsertoegang tot Office 365 en andere met Azure AD verbonden cloud-apps beperken met behulp van de Intune Managed Browser als goedgekeurde app.

U kunt nu de volgende voorwaarde configureren voor voorwaardelijke toegang op basis van toepassingen:

**Client-apps:** Browser

**Wat is het effect van de wijziging?**

Op dit moment wordt de toegang geblokkeerd wanneer u deze voorwaarde gebruikt. Wanneer de preview beschikbaar is, is voor alle toegang het gebruik van de beheerde-browsertoepassing vereist.

Zoek naar deze mogelijkheid en meer informatie in toekomstige blogs en opmerkingen bij de release.

Zie Voorwaardelijke toegang [in Azure AD voor meer informatie.](../conditional-access/overview.md)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-apps

**Type:** Plannen voor het wijzigen **van de servicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identiteitsbeveiliging en -beveiliging

De volgende apps staan in de lijst met [goedgekeurde client-apps:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Zie voor meer informatie:

- [Vereiste goedgekeurde client-app](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-apps](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Ondersteuning voor gebruiksvoorwaarden voor meerdere talen

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

Beheerders kunnen nu nieuwe gebruiksvoorwaarden maken die meerdere PDF-documenten bevatten. U kunt deze PDF-documenten taggen met een bijbehorende taal. Gebruikers krijgen de PDF te zien met de overeenkomende taal op basis van hun voorkeuren. Als er geen overeenkomst is, wordt de standaardtaal weergegeven.

---

### <a name="real-time-password-writeback-client-status"></a>Clientstatus voor het in realtime terugschrijven van wachtwoorden

**Type:** Nieuwe **functieservicecategorie:** Selfservice voor wachtwoord opnieuw **instellen Productmogelijkheid:** Gebruikersverificatie

U kunt nu de status van uw on-premises client voor het terugschrijven van wachtwoorden controleren. Deze optie is beschikbaar in de **sectie On-premises integratie** van de [pagina Wachtwoord opnieuw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) instellen.

Als er problemen zijn met uw verbinding met uw on-premises terugschrijvenclient, ziet u een foutbericht met het volgende:

- Informatie over waarom u geen verbinding kunt maken met uw on-premises terugschrijvenclient.
- Een koppeling naar documentatie die u helpt bij het oplossen van het probleem.

Zie [on-premises](../authentication/concept-sspr-howitworks.md#on-premises-integration)integratie voor meer informatie.

---

### <a name="azure-ad-app-based-conditional-access"></a>Voorwaardelijke toegang op basis van Azure AD-apps

**Type:** Nieuwe **functieservicecategorie:** Azure **AD-productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

U kunt nu de toegang tot Office 365 en andere met Azure AD verbonden cloud-apps beperken tot goedgekeurde [client-apps](../conditional-access/concept-conditional-access-conditions.md#client-apps) die ondersteuning bieden voor Intune-beleid voor [app-beveiliging](../conditional-access/app-based-conditional-access.md)met behulp van voorwaardelijke toegang op basis van azure AD-apps. Intune-beveiligingsbeleid voor apps wordt gebruikt om bedrijfsgegevens op deze clienttoepassingen te configureren en te beveiligen.

Door op [apps gebaseerd beleid voor](../conditional-access/app-based-conditional-access.md) voorwaardelijke toegang op basis van apparaten te combineren, hebt u de flexibiliteit om gegevens voor persoonlijke en bedrijfsapparaten te beveiligen. [](../conditional-access/require-managed-devices.md)

De volgende voorwaarden en besturingselementen zijn nu beschikbaar voor gebruik met voorwaardelijke toegang op basis van apps:

**Ondersteunde platformvoorwaarde**

- iOS
- Android

**Voorwaarde voor client-apps**

- Mobiele apps en desktop-clients

**Toegangsbeheer**

- Goedgekeurde client-apps vereisen

Zie Op apps gebaseerde voorwaardelijke toegang van [Azure AD voor meer informatie.](../conditional-access/app-based-conditional-access.md)

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-apparaten beheren in de Azure Portal

**Type:** Nieuwe **functieservicecategorie: Apparaatregistratie** en -beheer **Productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

U kunt nu al uw apparaten die zijn verbonden met Azure AD en de apparaatgerelateerde activiteiten op één plek vinden. Er is een nieuwe beheerervaring voor het beheren van al uw apparaat-id's en -instellingen in de Azure Portal. In deze release kunt u het volgende doen:

- Alles weergeven apparaten die beschikbaar zijn voor voorwaardelijke toegang in Azure AD.
- Eigenschappen weergeven, waaronder uw hybride Azure AD-apparaten.
- Zoek BitLocker-sleutels voor uw azure AD-apparaten, beheer uw apparaat met Intune en meer.
- Azure AD-apparaatgerelateerde instellingen beheren.

Zie Apparaten beheren met behulp van de Azure Portal [voor meer Azure Portal.](../devices/device-management-azure-portal.md)

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Ondersteuning voor macOS als apparaatplatform voor voorwaardelijke toegang van Azure AD

**Type:** Nieuwe **functieServicecategorie:** **Productmogelijkheid voor voorwaardelijke toegang:** Identiteitsbeveiliging en -beveiliging

U kunt macOS nu opnemen (of uitsluiten) als een apparaatplatformvoorwaarde in uw beleid voor voorwaardelijke toegang van Azure AD. Met de toevoeging van macOS aan de ondersteunde apparaatplatforms kunt u het volgende doen:

- **MacOS-apparaten inschrijven en beheren met Behulp van Intune.** Net als bij andere platformen, zoals iOS en Android, is er een bedrijfsportaltoepassing beschikbaar voor macOS voor het doen van geïntegreerde inschrijvingen. U kunt de nieuwe bedrijfsportal-app voor macOS gebruiken om een apparaat bij Intune in te schrijven en te registreren bij Azure AD.
- **Zorg ervoor dat macOS-apparaten voldoen aan het nalevingsbeleid van uw organisatie dat is gedefinieerd in Intune.** In Intune op de Azure Portal kunt u nu nalevingsbeleid instellen voor macOS-apparaten.
- **Beperk de toegang tot toepassingen in Azure AD tot alleen compatibele macOS-apparaten.** Bij het schrijven van beleid voor voorwaardelijke toegang is macOS een afzonderlijke platformoptie voor apparaten. U kunt nu macOS-specifiek beleid voor voorwaardelijke toegang schrijven voor de doeltoepassing die is ingesteld in Azure.

Zie voor meer informatie:

- [Een apparaatnalevingsbeleid maken voor macOS-apparaten in Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Voorwaardelijke toegang in Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Network Policy Server-extensie voor Azure AD Multi-Factor Authentication

**Type:** Nieuwe **functieservicecategorie:**  Multi-Factor **Authentication-productmogelijkheid:** Gebruikersverificatie

De Network Policy Server-extensie voor Azure AD Multi-Factor Authentication voegt multi-factor authentication-mogelijkheden in de cloud toe aan uw verificatie-infrastructuur met behulp van uw bestaande servers. Met de Network Policy Server-extensie kunt u telefoonoproepen, sms-berichten of verificatie via een telefoon-app toevoegen aan uw bestaande verificatiestroom. U hoeft geen nieuwe servers te installeren, configureren en onderhouden.

Deze extensie is gemaakt voor organisaties die verbindingen van virtuele particuliere netwerken willen beveiligen zonder de Azure-Multi-Factor Authentication-server. De Network Policy Server fungeert als een adapter tussen RADIUS en Azure AD Multi-Factor Authentication in de cloud om een tweede verificatiefactor te bieden voor federatief of gesynchroniseerde gebruikers.

Zie Uw bestaande Network Policy Server [integreren met Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md)voor meer informatie.

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Verwijderde gebruikers herstellen of definitief verwijderen

**Type:** Nieuwe **functieservicecategorie:** Productmogelijkheid voor **gebruikersbeheer:** Directory

In het Azure AD-beheercentrum kunt u nu het volgende doen:

- Een verwijderde gebruiker herstellen.
- Een gebruiker permanent verwijderen.

**Ga als volgende te werk om het uit te proberen:**

1. Selecteer in het Azure AD-beheercentrum [de optie Alle gebruikers](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) in de **sectie** Beheren.

2. Selecteer onlangs **verwijderde** gebruikers **in de lijst Tonen.**

3. Selecteer een of meer onlangs verwijderde gebruikers en herstel deze of verwijder ze definitief.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nieuwe goedgekeurde client-apps voor voorwaardelijke toegang op basis van Azure AD-apps

**Type:** **Functieservicecategorie gewijzigd: Voorwaardelijke** toegang **Productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

De volgende apps zijn toegevoegd aan de lijst met [goedgekeurde client-apps:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft Planner
- Azure Information Protection

Zie voor meer informatie:

- [Vereiste voor goedgekeurde client-app](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Voorwaardelijke toegang op basis van Azure AD-app](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>'OR' gebruiken tussen besturingselementen in een beleid voor voorwaardelijke toegang

**Type:** **Functieservicecategorie gewijzigd: Voorwaardelijke** toegang **Productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

U kunt nu 'OR' (een van de geselecteerde besturingselementen vereisen) gebruiken voor de besturingselementen voor voorwaardelijke toegang. U kunt deze functie gebruiken om beleid te maken met 'OR' tussen toegangsbesturingselementen. U kunt deze functie bijvoorbeeld gebruiken om een beleid te maken dat vereist dat een gebruiker zich moet aanmelden met behulp van Multi-Factor Authentication 'OR' om zich op een compatibel apparaat te kunnen aanmelden.

Zie Besturingselementen [in Voorwaardelijke toegang van Azure AD voor meer informatie.](../conditional-access/controls.md)

---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregatie van realtime risicodetecties

**Type:** **Functieservicecategorie gewijzigd:** Productmogelijkheid identiteitsbeveiliging: Identiteitsbeveiliging en -beveiliging 

In Azure AD Identity Protection worden alle realtime risicodetecties die afkomstig zijn van hetzelfde IP-adres op een bepaalde dag nu geaggregeerd voor elk type risicodetectie. Deze wijziging beperkt het aantal risicodetecties dat wordt weergegeven zonder enige wijziging in de gebruikersbeveiliging.

De onderliggende realtime-detectie werkt telkens als de gebruiker zich aanmeldt. Als u een beveiligingsbeleid voor aanmeldingsrisico's hebt ingesteld op Multi-Factor Authentication of toegang blokkeert, wordt het nog steeds geactiveerd tijdens elke riskante aanmelding.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD-rapporten afvangen

**Type:** Plan for change **Service category:** Reporting **Product capability:** Identity Lifecycle Management

De Azure Portal biedt u het volgende:

- Een nieuwe Azure AD-beheerconsole.
- Nieuwe API's voor activiteiten- en beveiligingsrapporten.

Vanwege deze nieuwe mogelijkheden zijn de rapport-API's onder het eindpunt /reports op 10 december 2017 ingetrokken.

---

### <a name="automatic-sign-in-field-detection"></a>Automatische detectie van aanmeldingsveld

**Type:** Opgeloste **servicecategorie:** Mijn apps **Product-mogelijkheid:** Een aanmelding

Azure AD ondersteunt automatische detectie van aanmeldingsveld voor toepassingen die een HTML-gebruikersnaam en wachtwoordveld renderen. Deze stappen worden beschreven in Aanmeldingsvelden voor een [toepassing automatisch vastleggen.](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app) U vindt deze mogelijkheid door een toepassing *buiten* de galerie toe te voegen op de pagina **Bedrijfstoepassingen** in [Azure Portal](https://aad.portal.azure.com). Daarnaast kunt u de  modus Voor een aanmelding voor deze nieuwe toepassing configureren in Een aanmelding op basis van een wachtwoord, een **web-URL** invoeren en de pagina vervolgens opslaan.

Vanwege een serviceprobleem is deze functionaliteit tijdelijk uitgeschakeld. Het probleem is opgelost en de automatische detectie van aanmeldingsveld is weer beschikbaar.

---

### <a name="new-multi-factor-authentication-features"></a>Nieuwe functies van Multi-Factor Authentication

**Type:** Nieuwe **functieservicecategorie:** Multi-Factor **Authentication-productmogelijkheid:** Identiteitsbeveiliging en -beveiliging

Meervoudige verificatie (MFA) is een essentieel onderdeel van de beveiliging van uw organisatie. Om referenties adaptiefer te maken en de ervaring naadlooser te maken, zijn de volgende functies toegevoegd:

- Resultaten van meervoudige vraag worden rechtstreeks geïntegreerd in het Azure AD-aanmeldingsrapport, dat programmatische toegang tot MFA-resultaten bevat.
- De MFA-configuratie is dieper geïntegreerd in de Azure AD-configuratie-ervaring in de Azure Portal.

Met deze openbare preview zijn MFA-beheer en -rapportage een geïntegreerd onderdeel van de belangrijkste Azure AD-configuratie-ervaring. U kunt nu de functionaliteit van de MFA-beheerportal beheren binnen de Azure AD-ervaring.

Zie Naslaginformatie voor [MFA-rapportage in](../authentication/howto-mfa-reporting.md)de Azure Portal.

---

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

**Type:** Nieuwe **functieservicecategorie: Gebruiksrechtovereenkomst** **productmogelijkheid:** Naleving

U kunt de gebruiksvoorwaarden van Azure AD gebruiken om informatie weer te geven, zoals relevante disclaimers voor juridische vereisten of nalevingsvereisten voor gebruikers.

U kunt de gebruiksvoorwaarden van Azure AD gebruiken in de volgende scenario's:

- Algemene gebruiksvoorwaarden voor alle gebruikers in uw organisatie
- Specifieke gebruiksvoorwaarden op basis van de kenmerken van een gebruiker (bijvoorbeeld artsen versus verantwoordelijken of nationale werknemers versus internationale werknemers, uitgevoerd door dynamische groepen)
- Specifieke gebruiksvoorwaarden voor toegang tot zakelijke apps met een hoge impact, zoals Salesforce

Zie Azure [AD-gebruiksvoorwaarden voor meer informatie.](../conditional-access/terms-of-use.md)

---

### <a name="enhancements-to-privileged-identity-management"></a>Verbeteringen aan Privileged Identity Management

**Type:** Nieuwe **functieservicecategorie: Privileged Identity Management** **productmogelijkheid:** Privileged Identity Management

Met Azure AD Privileged Identity Management kunt u de toegang tot Azure-resources (preview) binnen uw organisatie beheren, beheren en bewaken voor het volgende:

- Abonnementen
- Resourcegroepen
- Virtuele machines

Alle resources binnen de Azure Portal die gebruikmaken van de Azure RBAC-functionaliteit, kunnen profiteren van alle beveiligings- en levenscyclusbeheermogelijkheden die Azure AD Privileged Identity Management te bieden heeft.

Zie Azure-resources Privileged Identity Management [meer informatie.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="access-reviews"></a>Toegangsbeoordelingen

**Type:** Nieuwe **functieServicecategorie:** Toegangsbeoordelingen **Productmogelijkheid:** Naleving

Organisaties kunnen toegangsbeoordelingen (preview) gebruiken om groepslidmaatschap en toegang tot bedrijfstoepassingen efficiënt te beheren:

- U kunt toegang voor gastgebruikers opnieuw certificeren door gebruik te maken van toegangsbeoordelingen van hun toegang tot toepassingen en lidmaatschappen van groepen. Revisoren kunnen efficiënt bepalen of gasten toegang moeten blijven verlenen op basis van de inzichten die worden geboden door de toegangsbeoordelingen.
- Met toegangsbeoordelingen kunt u de toegang van werknemers voor toepassingen en groepslidmaatschappen opnieuw certificeren.

U kunt de controles van toegangsbeoordelingen verzamelen in programma's die relevant zijn voor uw organisatie, om beoordelingen bij te houden voor naleving of risicogevoelige toepassingen.

Zie Azure [AD-toegangsbeoordelingen voor meer informatie.](../governance/access-reviews-overview.md)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Toepassingen van derden verbergen in Mijn apps en het start programma voor Office 365-apps

**Type:** Nieuwe **functieservicecategorie: Mijn apps** **Product-mogelijkheid:** Een aanmelding

U kunt nu beter apps beheren die worden weergeven in de portals van uw gebruikers via een nieuwe **eigenschap voor het verbergen van apps.** U kunt apps verbergen om te helpen in gevallen waarin app-tegels worden weergeven voor back-endservices of dubbele tegels en rommelige startprogramma's voor apps van gebruikers. De schakelknop staat in de **sectie Eigenschappen** van de app van derden en heeft het label Zichtbaar voor **de gebruiker?** U kunt een app ook programmatisch verbergen via PowerShell.

Zie Een toepassing van derden verbergen voor de gebruikerservaring [in Azure AD voor meer informatie.](../manage-apps/hide-application-from-user-portal.md)


**Wat is er beschikbaar?**

 Als onderdeel van de overgang naar de nieuwe beheerconsole zijn er twee nieuwe API's beschikbaar voor het ophalen van Azure AD-activiteitenlogboeken. De nieuwe set API's biedt uitgebreidere filter- en sorteerfunctionaliteit, naast uitgebreidere controle- en aanmeldingsactiviteiten. De gegevens die eerder beschikbaar waren via de beveiligingsrapporten, zijn nu toegankelijk via de Identity Protection Risk Detections-API in Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix voor Identity Manager

**Type:** **Functieservicecategorie gewijzigd:** Identity **Manager-productmogelijkheid:** Beheer van identiteitslevenscyclus

Een hotfixpakket (build 4.4.1642.0) is beschikbaar vanaf 25 september 2017 voor Identity Manager 2016 Service Pack 1. Dit pakket voor samenrollen:

- Lost problemen op en voegt verbeteringen toe.
- Is een cumulatieve update die alle updates van Identity Manager 2016 Service Pack 1 vervangt tot build 4.4.1459.0 voor Identity Manager 2016.
- Hiervoor moet u Identity Manager 2016 build 4.4.1302.0 hebben.

Zie [Hotfix rollup package (build 4.4.1642.0) is available for Identity Manager 2016 Service Pack 1 (Hotfix-pakket (build 4.4.1642.0) is beschikbaar voor Identity Manager 2016 Service Pack 1.](https://support.microsoft.com/help/4021562)

---