---
title: Veelgestelde vragen over B2B-samen werking-Azure Active Directory | Microsoft Docs
description: Krijg antwoorden op veelgestelde vragen over Azure Active Directory B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 09/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2825d3776f7b45f6726aaec05484900511ed1477
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172972"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Veelgestelde vragen over Azure Active Directory B2B-samen werking

Deze veelgestelde vragen (FAQ) over de samen werking tussen Azure Active Directory (Azure AD) met Business-to-Business (B2B) worden regel matig bijgewerkt, zodat er nieuwe onderwerpen worden toegevoegd.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021** heeft Microsoft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en -tenants voor B2B-samenwerkingsscenario's. In de voorbereiding raden wij klanten aan om te kiezen voor de [verificatie van de eenmalige wachtwoordcode e-mailen](one-time-passcode.md). We waarderen uw feedback over deze openbare preview-functie en willen graag nog meer manieren te maken om samen te werken.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kunnen we onze aanmeldings pagina aanpassen zodat deze meer intuïtief is voor onze B2B-samenwerkings gast gebruikers?
Rechthebbend! Bekijk onze [blog post over deze functie](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Voor meer informatie over het aanpassen van de aanmeldings pagina van uw organisatie, Zie [bedrijfs huisstijl toevoegen om u aan te melden en de pagina's van het toegangs venster](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Kunnen B2B-samenwerkings gebruikers toegang krijgen tot share point online en OneDrive?
Ja. Het is echter wel mogelijk om te zoeken naar bestaande gast gebruikers in share point online met behulp **van de** kiezer personen. Als u de optie wilt inschakelen om te zoeken naar bestaande gast gebruikers, stelt u **ShowPeoplePickerSuggestionsForGuestUsers** in **op** aan. U kunt deze instelling inschakelen op het niveau van de Tenant of op het niveau van de site verzameling. U kunt deze instelling wijzigen met behulp van de cmdlets Set-SPOTenant en Set-SPOSite. Met deze cmdlets kunnen leden alle bestaande gast gebruikers in de Directory doorzoeken. Wijzigingen in het Tenant bereik hebben geen invloed op share point online-sites die al zijn ingericht.

### <a name="is-the-csv-upload-feature-still-supported"></a>Wordt de functie voor het uploaden van CSV nog steeds ondersteund?
Ja. Zie [Dit Power shell](code-samples.md)-voor beeld voor meer informatie over het gebruik van de functie voor het uploaden van CSV-bestanden.

### <a name="how-can-i-customize-my-invitation-emails"></a>Hoe kan ik mijn e-mail berichten aanpassen?
U kunt bijna alles over het uitnodigings proces aanpassen met behulp van de [API voor B2B-uitnodiging](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Kunnen gast gebruikers hun multi-factor Authentication-methode opnieuw instellen?
Ja. Gast gebruikers kunnen hun multi-factor Authentication-methode op dezelfde manier opnieuw instellen als normale gebruikers.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Welke organisatie is verantwoordelijk voor multi-factor Authentication-licenties?
De uitnodigende organisatie voert multi-factor Authentication uit. De uitnodigings organisatie moet ervoor zorgen dat de organisatie voldoende licenties heeft voor hun B2B-gebruikers die gebruikmaken van multi-factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Wat gebeurt er als voor een partner organisatie al multi-factor Authentication is ingesteld? Kunnen we hun multi-factor Authentication vertrouwen en gebruiken we niet onze eigen multi-factor Authentication?
Deze functie wordt momenteel niet ondersteund. Als voor toegang tot de resources van uw organisatie multi-factor Authentication is vereist, moet de partner organisatie zich registreren voor multi-factor Authentication in uw organisatie (de uitnodigingen).

### <a name="how-can-i-use-delayed-invitations"></a>Hoe kan ik uitgestelde uitnodigingen gebruiken?
Een organisatie kan B2B-samenwerkings gebruikers toevoegen, ze indien nodig inrichten voor toepassingen en vervolgens uitnodigingen verzenden. U kunt de API voor B2B-samen werking gebruiken om de onboarding-werk stroom aan te passen.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan ik gast gebruikers zichtbaar maken in de algemene adres lijst van Exchange?
Ja. Gast objecten zijn standaard niet zichtbaar in de algemene adres lijst van uw organisatie, maar u kunt Azure Active Directory Power shell gebruiken om ze zichtbaar te maken. Zie [kan ik gast objecten zichtbaar maken in de algemene adres lijst?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan ik een beperkte beheerder van een gast gebruiker maken?
Absoluut. Zie voor meer informatie [gast gebruikers toevoegen aan een rol](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Biedt Azure AD B2B-samen werking B2B-gebruikers toegang tot de Azure Portal?
Tenzij aan een gebruiker de rol van beperkte beheerder is toegewezen, is voor B2B-samenwerkings gebruikers geen toegang tot de Azure Portal vereist. B2B-samenwerkings gebruikers aan wie de rol van beperkte beheerder is toegewezen, hebben echter toegang tot de portal. Als een gast gebruiker aan wie niet een van deze beheerders rollen is toegewezen, toegang heeft tot de portal, kan de gebruiker mogelijk toegang krijgen tot bepaalde onderdelen van de ervaring. De gast gebruikersrol heeft enkele machtigingen in de Directory.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan ik de toegang tot de Azure Portal voor gast gebruikers blok keren?

Ja. U kunt een beleid voor voorwaardelijke toegang maken waarmee wordt voor komen dat alle gasten en externe gebruikers toegang krijgen tot de Azure Portal. Wanneer u dit beleid configureert, moet u voorzichtig zijn om te voor komen dat de toegang tot leden en beheerders per ongeluk wordt geblokkeerd.

1. Meld u als beveiligingsbeheerder of beheerder voor voorwaardelijke toegang aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in Azure Portal **Azure Active Directory**. 
3. Selecteer onder **beheren** de optie **beveiliging**.
4. Onder **beveiligen** selecteert u **voorwaardelijke toegang**. Selecteer **Nieuw beleid**.
5. Voer op de pagina **Nieuw** in het tekstvak **naam** een naam in voor het beleid (bijvoorbeeld ' gasten blok keren voor toegang tot de portal ').
6. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
7. Klik op het tabblad **include** op **gebruikers en groepen selecteren** en selecteer vervolgens **alle gast en externe gebruikers (preview-versie)**.
9. Selecteer **Gereed**.
10. Selecteer op de pagina **Nieuw** in de sectie **toewijzingen** de optie **Cloud-apps of-acties**.
11. Kies op de pagina **Cloud-apps of acties** de optie **apps selecteren** en kies vervolgens **selecteren**.
12. Kies op de pagina **Selecteren** de optie **Microsoft Azure Management** en kies vervolgens **Selecteren**.
13. Op de pagina **Cloud-apps of acties** , selecteer **gereed**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Ondersteunt Azure AD B2B-samen werking multi-factor Authentication-en consumenten-e-mail accounts?
Ja. Multi-factor Authentication-en consumenten-e-mail accounts worden beide ondersteund voor Azure AD B2B-samen werking.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Ondersteunt u het opnieuw instellen van wacht woorden voor Azure AD B2B-samenwerkings gebruikers?
Als uw Azure AD-Tenant de basis directory voor een gebruiker is, kunt u [het wacht woord van de gebruiker opnieuw instellen](../fundamentals/active-directory-users-reset-password-azure-portal.md) via de Azure Portal. Maar u kunt een wacht woord niet rechtstreeks opnieuw instellen voor een gast gebruiker die zich aanmeldt met een account dat wordt beheerd door een andere Azure AD-adres lijst of een externe ID-provider. Alleen de gast gebruiker of een beheerder in de basismap van de gebruiker kan het wacht woord opnieuw instellen. Hier volgen enkele voor beelden van hoe het opnieuw instellen van wacht woorden werkt voor gast gebruikers:
 
* Gast gebruikers die zich aanmelden met een Microsoft-account (bijvoorbeeld guestuser@live.com ) kunnen hun eigen wacht woorden opnieuw instellen met behulp van Microsoft-account self-service voor wachtwoord herstel (SSPR). Lees [hoe u uw Microsoft-account wacht woord opnieuw instelt](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Gast gebruikers die zich aanmelden met een Google-account of een andere externe ID-provider kunnen hun eigen wacht woord opnieuw instellen met behulp van de SSPR-methode van de identiteits provider. Een gast gebruiker met het Google-account kan bijvoorbeeld guestuser@gmail.com het wacht woord opnieuw instellen door de instructies te volgen in [uw wacht woord wijzigen of opnieuw instellen](https://support.google.com/accounts/answer/41078).
* Als de identiteits Tenant een just-in-time-Tenant (JIT) of ' virale ' is (wat betekent dat het een afzonderlijke, niet-beheerde Azure-Tenant is), kan alleen de gast gebruiker het wacht woord opnieuw instellen. Soms neemt een organisatie het [beheer van virale tenants over](../enterprise-users/domains-admin-takeover.md) die worden gemaakt wanneer werk nemers hun werk-e-mail adressen gebruiken om zich aan te melden voor services. Nadat de organisatie een virale Tenant heeft overgenomen, kan alleen een beheerder in die organisatie het wacht woord van de gebruiker opnieuw instellen of SSPR inschakelen. Als dat nodig is, kunt u, als de uitnodigende organisatie, het gast gebruikers account uit uw Directory verwijderen en een uitnodiging opnieuw verzenden.

* Als de basismap van de gast gebruiker uw Azure AD-Tenant is, kunt u het wacht woord van de gebruiker opnieuw instellen. U hebt bijvoorbeeld een gebruiker gemaakt of een gebruiker gesynchroniseerd vanaf uw on-premises Active Directory en de User type ingesteld op gast. Omdat deze gebruiker zich in uw directory bevindt, kunt u het wacht woord opnieuw instellen via de Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Biedt micro soft Dynamics 365 online ondersteuning voor Azure AD B2B-samen werking?
Ja, Dynamics 365 (online) biedt ondersteuning voor Azure AD B2B-samen werking. Zie voor meer informatie het artikel Dynamics 365 [gebruikers uitnodigen met Azure AD B2B-samen werking](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Wat is de levens duur van een eerste wacht woord voor een nieuw gemaakte B2B-samenwerkings gebruiker?
Azure AD heeft een vaste set tekens, wachtwoord sterkte en account vergrendelings vereisten die gelijk zijn voor alle Azure AD-Cloud gebruikers accounts. Cloud gebruikers accounts zijn accounts die niet zijn federatief met een andere ID-provider, zoals 
* Microsoft-account
* Facebook
* Active Directory Federation Services
* Een andere Cloud Tenant (voor B2B-samen werking)

Voor federatieve accounts is wachtwoord beleid afhankelijk van het beleid dat wordt toegepast op de on-premises pacht en de Microsoft-account-instellingen van de gebruiker.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Een organisatie wil mogelijk verschillende ervaringen hebben in hun toepassingen voor Tenant gebruikers en gast gebruikers. Zijn er standaard richtlijnen voor dit? Is de aanwezigheid van de ID-provider claim het juiste model om te gebruiken?
Een gast gebruiker kan een id-provider gebruiken voor verificatie. Zie [Eigenschappen van een B2B-samenwerkings gebruiker](user-properties.md)voor meer informatie. Gebruik de eigenschap **User type** om de gebruikers ervaring te bepalen. De claim **User type** is momenteel niet opgenomen in het token. Toepassingen moeten de Microsoft Graph-API gebruiken om de map op te vragen voor de gebruiker en om de User type op te halen.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Waar kan ik een community voor B2B-samen werking vinden om oplossingen te delen en ideeën te verzenden?
U luistert voortdurend naar uw feedback om B2B-samen werking te verbeteren. Deel uw gebruikers scenario's, aanbevolen procedures en wat u graag hebt over Azure AD B2B-samen werking. Word lid van de discussie in de [micro soft tech-Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
We nodigen u ook uit om uw ideeën in te dienen en te stemmen voor toekomstige functies bij [B2B-samenwerkings ideeën](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kunnen we een uitnodiging verzenden die automatisch wordt ingewisseld, zodat de gebruiker alleen ' klaar voor de go ' is? Of hoeft de gebruiker altijd door te klikken met de aflosss-URL?
U kunt andere gebruikers in de partner organisatie uitnodigen door gebruik te maken van de gebruikers interface, Power shell-scripts of Api's. U kunt de gast gebruiker vervolgens een directe koppeling naar een gedeelde app sturen. In de meeste gevallen is het niet meer nodig om de uitnodiging voor e-mail te openen en op een opname-URL te klikken. Zie [Azure Active Directory uitnodiging voor B2B-samen werking](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hoe werkt B2B-samen werking wanneer de genodigde partner Federatie gebruikt om hun eigen on-premises verificatie toe te voegen?
Als de partner een Azure AD-Tenant heeft die federatief is voor de on-premises verificatie-infra structuur, wordt on-premises eenmalige aanmelding (SSO) automatisch behaald. Als de partner geen Azure AD-Tenant heeft, wordt er een Azure AD-account gemaakt voor nieuwe gebruikers. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Ik dacht dat Azure AD B2B geen gmail.com-en outlook.com-e-mail adressen heeft geaccepteerd en dat B2C voor dit soort accounts werd gebruikt?
We verwijderen de verschillen tussen B2B-en Business-to-consumer (B2C)-samen werking in termen van welke identiteiten worden ondersteund. De identiteit die wordt gebruikt, is niet een goede reden om te kiezen tussen het gebruik van B2B of het gebruik van B2C. Zie [B2B-samen werking en B2C vergelijken in azure Active Directory](compare-with-b2c.md)voor meer informatie over het kiezen van uw samenwerkings optie.

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Kan een Azure AD B2C lokaal account worden uitgenodigd voor een Azure AD-Tenant voor B2B-samen werking?
Nee. Een Azure AD B2C lokale account kan alleen worden gebruikt om u aan te melden bij de Azure AD B2C-Tenant. Het account kan niet worden gebruikt om u aan te melden bij een Azure AD-Tenant. Het uitnodigen van een Azure AD B2C lokale account bij een Azure AD-Tenant voor B2B-samen werking wordt niet ondersteund.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Welke toepassingen en services ondersteunen Azure B2B-gast gebruikers?
Alle met Azure AD geïntegreerde toepassingen kunnen ondersteuning bieden voor Azure B2B-gast gebruikers, maar ze moeten een endpoint instellen als Tenant om gast gebruikers te verifiëren. Mogelijk moet u ook [de claims aanpassen](claims-mapping.md) in het SAML-token dat wordt uitgegeven wanneer een gast gebruiker zich verifieert bij de app. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kunnen we multi-factor Authentication afdwingen voor B2B-gast gebruikers als onze partners geen multi-factor Authentication hebben?
Ja. Zie [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md)voor meer informatie.

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>In share point kunt u een lijst "toestaan" of "weigeren" definiëren voor externe gebruikers. Kunnen we dit doen in azure?
Ja. Azure AD B2B-samen werking ondersteunt het toestaan van lijsten en het weigeren van lijsten. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Welke licenties moeten er worden gebruikt voor Azure AD B2B?
Zie [prijzen van externe identiteit](external-identities-pricing.md)voor informatie over de licenties die uw organisatie nodig heeft om Azure AD B2B te gebruiken.

### <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)