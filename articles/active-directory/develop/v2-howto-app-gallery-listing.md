---
title: Uw app publiceren in de galerie met Azure Active Directory apps
description: Meer informatie over het weer geven van een toepassing die eenmalige aanmelding in de app-galerie van Azure Active Directory ondersteunt.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 396d6f69673f8758d8d1302f8d10b8a92e5f50b4
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530746"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Uw app publiceren in de app-galerie van Azure AD

U kunt uw app publiceren in de app-galerie van Azure AD. Wanneer uw app wordt gepubliceerd, wordt deze weer gegeven als een optie voor klanten wanneer ze apps toevoegen aan hun Tenant. 

Enkele voor delen van het toevoegen van uw app aan de Azure AD-galerie zijn:

- Klanten vinden de best mogelijke eenmalige aanmelding voor uw app.
- De configuratie van de toepassing is eenvoudig en mini maal.
- Met een snelle zoek opdracht vindt u uw toepassing in de galerie.
- Gratis, Basic en Premium Azure AD-klanten kunnen deze integratie gebruiken.
- Wederzijdse klanten krijgen een stapsgewijze zelf studie voor configuratie.

Daarnaast zijn er veel voor delen wanneer uw klanten Azure AD als id-provider voor uw app gebruiken. Dit zijn onder andere:

- Bieden van eenmalige aanmelding voor uw gebruikers. Met eenmalige aanmelding vermindert u de ondersteunings kosten door het voor uw klanten gemakkelijker te maken om eenmalige aanmelding te bieden. Als eenmalige aanmelding is ingeschakeld, hebben de IT-beheerders van uw klanten geen informatie over het configureren van uw toepassing voor gebruik in hun organisatie. Zie [Wat is eenmalige aanmelding?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over eenmalige aanmelding.
- Uw app kan worden gedetecteerd in de galerie met Microsoft 365 apps, het start programma voor Microsoft 365 apps en in micro soft Search op Office.com. 
- Geïntegreerd app-beheer. Zie [Wat is toepassings beheer?](../manage-apps/what-is-application-management.md)voor meer informatie over app-beheer in azure AD.
- Uw app kan de [Graph API](/graph/) gebruiken om toegang te krijgen tot de gegevens die de gebruikers productiviteit in het micro soft-ecosysteem bewaart.
- Toepassingsspecifieke documentatie die in combi natie met het Azure AD-team is geproduceerd voor onze wederzijdse klanten, vereenvoudigt de acceptatie.
- U geeft uw klanten de mogelijkheid om de verificatie en autorisatie van hun werk nemers en gast identiteiten volledig te beheren.
- Het plaatsen van alle account beheer en nalevings verantwoordelijkheid met de klant eigenaar van deze identiteiten.
- Biedt de mogelijkheid om eenmalige aanmelding in of uit te scha kelen voor specifieke id-providers, groepen of gebruikers om te voldoen aan de behoeften van hun bedrijf.
- U kunt uw markt en goed keuring verg Roten. Veel grote organisaties vereisen dat (of streven ernaar) hun werk nemers naadloze SSO-ervaringen hebben in alle toepassingen. Het is belang rijk om SSO eenvoudig te maken.
- U vermindert de wrijving van de eind gebruiker, waardoor het gebruik van de eind gebruiker kan toenemen en uw omzet kan worden verhoogd.
- Klanten die het systeem voor Cross-Domain Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) gebruiken, kunnen inrichten voor dezelfde app gebruiken.
- Voeg beveiliging en gebruiks gemak toe wanneer gebruikers zich aanmelden bij toepassingen met behulp van Azure AD SSO en de nood zaak van afzonderlijke referenties verwijderen.

> [!TIP]
> Wanneer u uw toepassing aanbiedt voor gebruik door andere bedrijven via een aankoop of abonnement, maakt u uw toepassing beschikbaar voor klanten binnen hun eigen Azure-tenants. Dit staat bekend als het maken van een toepassing met meerdere tenants. Zie [multi tenant-toepassingen in azure](../../dotnet-develop-multitenant-applications.md) en [pacht in azure Active Directory](single-and-multi-tenant-apps.md)voor een overzicht van dit concept.

> [!IMPORTANT]
> Als u uw app in de Azure AD-galerie wilt publiceren, moet u akkoord gaan met specifieke voor waarden. Voordat u begint, moet u ervoor zorgen dat u de [voor waarden](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)leest en instemt.

De stappen voor het publiceren van uw app in de app-galerie van Azure AD zijn:
1. Kies de juiste eenmalige aanmelding voor uw app.
2. Implementeer eenmalige aanmelding in uw app.
3. Maak uw Azure-Tenant en test uw app.
4. Documentatie maken en publiceren.
5. Verzend uw app.
6. Word lid van het micro soft Partner Network.

## <a name="what-is-the-azure-ad-application-gallery"></a>Wat is de Azure AD-toepassings galerie?

- Klanten vinden de best mogelijke eenmalige aanmelding.
- De configuratie van de toepassing is eenvoudig en mini maal.
- Met een snelle zoek opdracht vindt u uw toepassing in de galerie.
- Gratis, Basic en Premium Azure AD-klanten kunnen deze integratie gebruiken.
- Wederzijdse klanten krijgen een stapsgewijze zelf studie voor configuratie.
- Klanten die het systeem voor Cross-Domain Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) gebruiken, kunnen inrichten voor dezelfde app gebruiken.

## <a name="prerequisites"></a>Vereisten

U hebt een permanent account nodig om te testen met ten minste twee gebruikers die zijn geregistreerd.

- Voor federatieve toepassingen (Open-ID en SAML/WS-voeder) moet de toepassing het SaaS-model (Software-as-a-Service) ondersteunen om te worden weer gegeven in de Azure AD-App-galerie. De Enter prise Gallery-toepassingen moeten ondersteuning bieden voor meerdere klant configuraties en niet voor een specifieke klant.
- Voor Open ID Connect moet de toepassing meerdere tenants zijn en moet het [Azure AD toestemmings raamwerk](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) goed worden geïmplementeerd voor de toepassing. De gebruiker kan de aanmeldings aanvraag naar een gemeen schappelijk eind punt verzenden, zodat elke klant toestemming kan geven voor de toepassing. U kunt de gebruikers toegang beheren op basis van de Tenant-ID en de UPN van de gebruiker die in het token is ontvangen.
- Voor SAML 2.0/WS-voeder moet uw toepassing de mogelijkheid hebben om de integratie van SAML/WS-invoerende SSO in de SP-of IDP-modus te kunnen uitvoeren. Zorg ervoor dat deze functie correct werkt voordat u de aanvraag indient.
- Zorg ervoor dat uw toepassing ondersteuning biedt voor formulier verificatie, zodat wachtwoord kluizen kan worden uitgevoerd om eenmalige aanmelding te kunnen werken.
- U hebt een permanent account nodig om te testen met ten minste twee gebruikers die zijn geregistreerd.

**Hoe kan ik Azure AD voor ontwikkel aars ophalen?**

U kunt een gratis test account krijgen met alle Premium Azure AD-functies-90 dagen gratis en kunnen worden uitgebreid zolang u de ontwikkel aars ermee kunt werken: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Stap 1: Kies de juiste eenmalige aanmelding voor uw app

Als u een toepassing in de Azure AD-App-galerie wilt weer geven, moet u ten minste één van de ondersteunde opties voor eenmalige aanmelding implementeren. Zie [SSO-opties](../manage-apps/sso-options.md)voor meer informatie over de opties voor eenmalige aanmelding en hoe klanten deze configureren in azure AD.

De volgende tabel vergelijkt de belangrijkste standaarden: open verificatie 2,0 (OAuth 2,0) met OpenID Connect Connect (OIDC), Security Assertion Markup Language (SAML) en Webservices-federatie (WS-started).

| Mogelijkheid| OAuth-OIDC| SAML/WS-Fed |
| - |-|-|
| Op het web gebaseerde eenmalige aanmelding| √| √ |
| Op het web gebaseerde eenmalige afmelding| √| √ |
| Eenmalige aanmelding op basis van mobiel| √| √* |
| Op mobiele apparaten gebaseerde eenmalige afmelding| √| √* |
| Beleid voor voorwaardelijke toegang voor mobiele toepassingen| √| X |
| Naadloze MFA-ervaring voor mobiele toepassingen| √| X |
| SCIM-inrichting| √| √ |
| Toegang tot Microsoft Graph krijgen| √| X |

* Mogelijk, maar micro soft biedt geen voor beelden of richt lijnen.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0- en OpenID Connect
OAuth 2,0 is een protocol voor de [industrie standaard](https://oauth.net/2/) voor autorisatie. OpenID Connect Connect (OIDC) is een [industrie standaard](https://openid.net/connect/) -identiteits verificatie laag die boven op het OAuth 2,0-protocol is gebouwd. 

**Redenen voor het kiezen van OAuth/OIDC**
- Met de autorisatie inherent aan deze protocollen kan uw toepassing toegang krijgen tot en worden geïntegreerd met uitgebreide gebruikers-en organisatie gegevens via de Microsoft Graph-API.
- Vereenvoudigt de gebruikers ervaring van uw klanten bij het aannemen van SSO voor uw toepassing. U kunt eenvoudig de benodigde machtigingen sets definiëren, die vervolgens automatisch worden weer gegeven aan de beheerder of de eind gebruiker.
- Met deze protocollen kunnen uw klanten beleid voor voorwaardelijke toegang en Multi-Factor Authentication (MFA) gebruiken om de toegang tot de toepassingen te beheren. 
- Micro soft biedt bibliotheken en [code voorbeelden op meerdere platformen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) om u te helpen bij het ontwikkelen.  

**Enkele dingen die u moet overwegen**
- Als u op SAML gebaseerde eenmalige aanmelding voor uw toepassing al hebt geïmplementeerd, wilt u mogelijk geen nieuwe standaard implementeren om uw app in de galerie op te halen.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 of WS-Fed

SAML is een volwassene en uitgebreide, [eenmalige aanmelding](https://www.oasis-open.org/standards#samlv2.0) voor webtoepassingen. Zie [hoe Azure het SAML-protocol gebruikt](active-directory-saml-protocol-reference.md)voor meer informatie over hoe Azure SAML gebruikt. 

Webservices-federatie (WS-voeder) is een [industrie standaard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) die algemeen wordt gebruikt voor webtoepassingen die zijn ontwikkeld met behulp van het .NET-platform.

**Redenen voor het kiezen van SAML**
- SAML 2,0 is een volwassene standaard en de meeste technologie platforms ondersteunen open-source bibliotheken voor SAML 2,0. 
- U kunt uw klanten een beheer interface bieden voor het configureren van SAML SSO. Ze kunnen SAML SSO configureren voor Microsoft Azure AD en elke andere ID-provider die SAML ondersteunt.

**Enkele dingen die u moet overwegen**
- Wanneer u SAML 2,0-of WSFed-protocollen gebruikt voor mobiele toepassingen, heeft bepaalde beleids regels voor voorwaardelijke toegang, waaronder multi-factor Authentication (MFA), een gedegradeerde ervaring.
- Als u toegang wilt tot de Microsoft Graph, moet u autorisatie implementeren via OAuth 2,0 om de benodigde tokens te genereren. 

### <a name="password-based"></a>Op basis van een wachtwoord
Met SSO op basis van wacht woorden, ook wel wachtwoord kluizen genoemd, kunt u gebruikers toegang en-wacht woorden beheren voor webtoepassingen die geen identiteits Federatie ondersteunen. Het is ook handig voor scenario's waarin meerdere gebruikers één account moeten delen, zoals de accounts voor de sociale media-app van uw organisatie.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Stap 2: eenmalige aanmelding in uw app implementeren
Elke app in de galerie moet een van de ondersteunde opties voor eenmalige aanmelding implementeren. Zie [SSO-opties](../manage-apps/sso-options.md)voor meer informatie over de ondersteunde opties.

Zie voor OAuth en OIDC [richt lijnen voor verificatie patronen](v2-app-types.md) en [Azure Active Directory-code voorbeelden](sample-v2-code.md).

Voor SAML en WS-voeding moet uw toepassing de mogelijkheid hebben om de SSO-integratie in de SP-of IDP-modus te kunnen uitvoeren. Zorg ervoor dat deze functie correct werkt voordat u de aanvraag indient.

Zie [Wat is verificatie?](../azuread-dev/v1-authentication-scenarios.md)voor meer informatie over verificatie.

> [!IMPORTANT]
> Voor federatieve toepassingen (OpenID Connect en SAML/WS-voeder) moet de app het SaaS-model (Software as a Service) ondersteunen. Azure AD Gallery-toepassingen moeten ondersteuning bieden voor meerdere klant configuraties en mogen niet specifiek zijn voor één klant.

### <a name="implement-oauth-20-and-openid-connect"></a>OAuth 2,0 en OpenID Connect Connect implementeren

Voor OpenID Connect Connect moet de toepassing meerdere tenants zijn en moet het [Azure AD toestemmings raamwerk](consent-framework.md) goed worden geïmplementeerd voor de toepassing. De gebruiker kan de aanmeldings aanvraag naar een gemeen schappelijk eind punt verzenden, zodat elke klant toestemming kan geven voor de toepassing. U kunt de gebruikers toegang beheren op basis van de Tenant-ID en de UPN van de gebruiker die in het token is ontvangen.

Zie [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor specifieke voor beelden. 

Zie voor specifieke voor beelden voor mobiele apparaten: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Universeel Windows-platform](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2,0 implementeren

Als uw app SAML 2,0 ondersteunt, kunt u deze rechtstreeks integreren met een Azure AD-Tenant. Zie op [SAML gebaseerde eenmalige aanmelding configureren](../manage-apps/configure-saml-single-sign-on.md)voor meer informatie over de SAML-configuratie met Azure AD.

Micro soft biedt geen-of aanbevolen bibliotheken voor SAML-implementaties. Er zijn veel open-source bibliotheken beschikbaar.

### <a name="implement-ws-fed"></a>WS-Fed implementeren
Zie [gebruikers verifiëren met WS-Federation in ASP.net core](/aspnet/core/security/authentication/ws-federation)voor meer informatie over WS-Fed in ASP.net core.

### <a name="implement-password-vaulting"></a>Wachtwoord kluizen implementeren

Maak een webtoepassing met een HTML-aanmeldings pagina. Zorg ervoor dat uw toepassing formulier verificatie ondersteunt, zodat wachtwoord kluizen kan worden uitgevoerd om eenmalige aanmelding te kunnen werken zoals verwacht.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>Stap 3: SCIM-gebruikers inrichting implementeren in uw app
Ondersteuning van [scim](https://aka.ms/scimoverview) -inrichting is een optioneel, maar niet aanbevolen stap voor het bouwen van uw toepassing. Het ondersteunen van de SCIM-standaard is eenvoudig te doen en stelt klanten in staat om automatisch gebruikers accounts in uw app te maken en bij te werken, zonder dat ze hoeven te vertrouwen op hand matige processen, zoals het uploaden van CSV-bestanden. Daarnaast kunnen klanten het verwijderen van gebruikers automatiseren en groepslid maatschappen synchroon houden. Dit kan niet worden gerealiseerd met een oplossing zoals SAML JIT. 

### <a name="learn-about-scim"></a>Meer informatie over SCIM
Zie voor meer informatie over de SCIM-standaarden en-voor delen voor uw klanten [inrichten met scim-aan de slag](https://aka.ms/scimoverview).

### <a name="understand-the-azure-ad-scim-implementation"></a>Meer informatie over de Azure AD SCIM-implementatie
Zie [een scim-eind punt bouwen en gebruikers inrichten met Azure AD configureren](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)voor meer informatie over de implementatie van Azure AD scim.

### <a name="implement-scim"></a>SCIM implementeren
Azure AD bevat [referentie code](https://aka.ms/scimoverview) om u te helpen bij het bouwen van een scim-eind punt. Er zijn ook bibliotheken van derden/verwijzingen die u kunt vinden op github.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>Stap 4: uw Azure-Tenant maken en uw app testen

U hebt een Azure AD-Tenant nodig om uw app te kunnen testen. Zie [Quick Start: een Tenant instellen](quickstart-create-new-tenant.md)om uw ontwikkel omgeving in te stellen.

Een Azure AD-Tenant wordt ook geleverd met elk Microsoft 365-abonnement. Als u een gratis Microsoft 365 ontwikkel omgeving wilt instellen, raadpleegt u [lid worden van het Microsoft 365 ontwikkelaars programma](/office/developer-program/microsoft-365-developer-program).

Zodra u een Tenant hebt, moet u eenmalige aanmelding en [inrichting](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)testen. 

**Voor OIDC-of Oath-toepassingen** [registreert u uw toepassing](quickstart-register-app.md) als multi tenant-toepassing. Selecteer de optie accounts in een organisatorische map en persoonlijke micro soft-accounts in ondersteunde account typen.

**Voor SAML-en WS-based-toepassingen** kunt u [eenmalige aanmelding op basis van SAML configureren](../manage-apps/configure-saml-single-sign-on.md) met behulp van een algemene SAML-sjabloon in azure AD.

U kunt indien nodig ook [een toepassing met één Tenant converteren naar meerdere tenants](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-5---create-and-publish-documentation"></a>Stap 5: Documentatie maken en publiceren

### <a name="documentation-on-your-site"></a>Documentatie op uw site

Gebruiks gemak is een belang rijke factor in beslissingen met betrekking tot bedrijfs software. Duidelijke, gemakkelijk te volgen documentatie ondersteunt uw klanten in hun reis traject en vermindert de ondersteunings kosten. Micro soft heeft gewerkt met duizenden software leveranciers.

Het is raadzaam dat uw documentatie op uw site mini maal de volgende items bevat.

* Inleiding tot uw SSO-functionaliteit
  * Ondersteunde protocollen
  * Versie en SKU
  * Lijst met ondersteunde id-providers met documentatie koppelingen
* Licentie gegevens voor uw toepassing
* Op rollen gebaseerd toegangs beheer voor het configureren van SSO
* Configuratie stappen voor eenmalige aanmelding
  * Configuratie-elementen van de gebruikers interface voor SAML met de verwachte waarden van de provider
  * Informatie over service providers die moeten worden door gegeven aan id-providers
* Indien OIDC/OAuth
  * Lijst met machtigingen die zijn vereist voor de toestemming met zakelijke redenen
* Stappen voor test gebruikers testen
* Probleemoplossings informatie, met inbegrip van fout codes en-berichten
* Ondersteunings mechanismen voor klanten
* Details over uw SCIM-eind punt, met inbegrip van de bronnen en kenmerken die worden ondersteund

### <a name="documentation-on-the-microsoft-site"></a>Documentatie op de micro soft-site

Wanneer u uw toepassing opneemt met de Azure Active Directory toepassings galerie, waarmee uw toepassing ook wordt gepubliceerd in de Azure Marketplace, wordt door micro soft documentatie gegenereerd voor de wederzijdse klanten die het stapsgewijze proces bespreken. [Hier](../saas-apps/tutorial-list.md)kunt u een voor beeld bekijken. Deze documentatie wordt gemaakt op basis van uw inzending naar de galerie, en u kunt deze eenvoudig bijwerken als u wijzigingen aanbrengt in uw toepassing met behulp van uw GitHub-account.


## <a name="step-6---submit-your-app"></a>Stap 6: uw app verzenden

Nadat u hebt gecontroleerd of uw toepassings integratie werkt met Azure AD, moet u de aanvraag voor de toepassing indienen in de portal van het [micro soft-toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

De eerste keer dat u zich probeert aan te melden bij de portal, wordt een van de volgende twee schermen weer gegeven. 

Als het bericht ' dat is niet werkt ' wordt weer gegeven, moet u contact opnemen met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Geef het e-mail account op dat u wilt gebruiken voor het verzenden van de aanvraag. Een zakelijk e-mail adres, bijvoorbeeld de `name@yourbusiness.com` voor keur. Het team van Azure AD voegt het account toe aan de portal van het micro soft-toepassings netwerk.

Als u de pagina ' toegang aanvragen ' ziet, vult u de zakelijke reden in en selecteert u **toegang aanvragen**.

Nadat het account is toegevoegd, kunt u zich aanmelden bij de portal van het micro soft-toepassings netwerk en de aanvraag indienen door de tegel **aanvraag indienen (ISV)** op de start pagina te selecteren.

![Tegel Request (ISV) verzenden op Start pagina](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemen bij het aanmelden bij de portal

Als deze fout optreedt tijdens het aanmelden, ziet u hier de details over het probleem en hoe u het kunt oplossen.

* Als uw aanmelding is geblokkeerd, zoals hieronder wordt weer gegeven:

  ![problemen met het omzetten van toepassingen in de galerie](./media/howto-app-gallery-listing/blocked.png)

**Wat gebeurt er:**

De gast gebruiker is federatief voor een thuis Tenant die ook een Azure AD is. De gast gebruiker heeft een hoog risico. Micro soft staat gebruikers met een hoog risico niet toe om toegang te krijgen tot de resources. Alle gebruikers met een hoog risico (werk nemers of gasten/leveranciers) moeten hun risico voor toegang tot micro soft-resources herstellen/sluiten. Voor gast gebruikers is dit risico van de gebruiker afkomstig van de thuis Tenant en is het beleid afkomstig van de resource Tenant (micro soft in dit geval).
 
**Veilige oplossingen:**

* Door MFA geregistreerde gast gebruikers herstellen hun eigen gebruikers risico. Dit kan door de gast gebruiker worden uitgevoerd om een beveiligd wacht woord te wijzigen of opnieuw in te stellen ( https://aka.ms/sspr) deze vereist MFA en SSPR bij de thuis Tenant). Het beveiligde wacht woord wijzigen of opnieuw instellen moet worden gestart op Azure AD en niet on-premises.

* Gast gebruikers hebben hun beheerders hun risico hersteld. In dit geval voert de beheerder een wachtwoord herstel uit (tijdelijke wacht woorden genereren). Dit vereist geen identiteits beveiliging. De beheerder van de gast gebruiker kan naar gaan https://aka.ms/RiskyUsers en op wacht woord opnieuw instellen klikken.

* Gast gebruikers hebben hun beheerders hun Risico's sluiten/verwijderen. Opnieuw, hiervoor is geen identiteits beveiliging vereist. De beheerder kan naar gaan https://aka.ms/RiskyUsers en klikken op risico van gebruikers negeren. De beheerder moet er echter rekening mee houden dat dit een onjuiste positieve risico analyse was voordat het gebruikers risico wordt gesloten. Anders zetten ze hun en micro soft-bronnen risico door een risico analyse zonder onderzoek te onderdrukken.

> [!NOTE]
> Als u problemen hebt met Access, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Implementatie-specifieke opties
Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van OpenID Connect Connect, selecteert u **OpenID Connect connect & OAuth 2,0** , zoals wordt weer gegeven.

![Een OpenID Connect Connect-toepassing in de galerie weer geven](./media/howto-app-gallery-listing/openid.png)

Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van **saml 2,0** of **WS-voeder**, selecteert u **SAML 2.0/WS-wordt gevoederd** zoals weer gegeven.

![Een SAML 2,0-of WS-Fed-toepassing in de galerie weer geven](./media/howto-app-gallery-listing/saml.png)

Als u uw toepassing wilt toevoegen aan de lijst in de galerie met behulp van wacht woord-SSO, selecteert u **wacht woord-SSO (gebruikers naam & wacht woord)** , zoals wordt weer gegeven.

![Een SSO-toepassing voor wacht woorden in de galerie weer geven](./media/howto-app-gallery-listing/passwordsso.png)

Als u een [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2,0-eind punt voor het inrichten van gebruikers implementeert, selecteert u de optie zoals weer gegeven. Wanneer u het schema in de voorbereidings aanvraag levert, volgt u de instructies [hier](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration) om uw schema te downloaden. We gebruiken het schema dat u hebt geconfigureerd bij het testen van de toepassing voor niet-galerijen om de galerie toepassing te bouwen. 

   ![Aanvraag voor gebruikers inrichting](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Een bestaande vermelding bijwerken of verwijderen

U kunt een bestaande galerie-app bijwerken of verwijderen in de portal van het [micro soft-toepassings netwerk](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Een SAML-toepassing in de galerie weer geven](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Als u problemen hebt met Access, raadpleegt u de vorige sectie over het maken van uw account. Als dat niet werkt, neemt u contact op met het [Azure AD SSO-integratie team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="list-requests-by-customers"></a>Aanvragen van klanten weer geven

Klanten kunnen een aanvraag indienen om een toepassing weer te geven door app-aanvragen te selecteren **door klanten**  >  **nieuwe aanvraag** in te dienen.

![Toont de tegel door de klant aangevraagde apps](./media/howto-app-gallery-listing/customer-submit-request.png)

Hier volgt de stroom van door de klant aangevraagde toepassingen.

![Toont de stroom van de door de klant aangevraagde apps](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>Tijdlijnen

De tijd lijn voor het proces van het weer geven van een SAML 2,0-of WS-Fed-toepassing in de galerie is 7 tot 10 werk dagen.

![Tijd lijn voor het weer geven van een SAML-toepassing in de galerie](./media/howto-app-gallery-listing/timeline.png)

De tijd lijn voor het proces van het weer geven van een OpenID Connect Connect-toepassing in de galerie is 2 tot 5 werk dagen.

![Tijd lijn voor het weer geven van een OpenID Connect Connect-toepassing in de galerie](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Escalaties

Voor eventuele escalaties verzendt u een e-mail naar het [Azure AD SSO-integratie team](mailto:SaaSApplicationIntegrations@service.microsoft.com)en wij zullen zo snel mogelijk reageren.


## <a name="step-7---join-the-microsoft-partner-network"></a>Stap 7: deel nemen aan het micro soft Partner Network
De Microsoft Partner Network biedt directe toegang tot exclusieve resources, Program ma's, hulpprogram ma's en verbindingen. Zie [commerciële klanten bereiken](https://partner.microsoft.com/explore/commercial#gtm)om lid te worden van het netwerk en uw Go to Market-abonnement te maken.


## <a name="next-steps"></a>Volgende stappen
* [Een SCIM-eind punt bouwen en gebruikers inrichten configureren](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Verificatie scenario's voor Azure AD](authentication-flows-app-scenarios.md)
