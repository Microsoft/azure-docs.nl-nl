---
title: Apps bouwen die zich aanmelden bij Azure AD-gebruikers
titleSuffix: Microsoft identity platform
description: Laat zien hoe u een multi tenant-toepassing bouwt waarmee een gebruiker kan worden aangemeld bij een wille keurige Azure Active Directory Tenant.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581717"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Procedure: Een Azure Active Directory-gebruiker aanmelden met behulp van het patroon voor multitenant-toepassingen

Als u een SaaS-toepassing (Software as a Service) aan een groot aantal organisaties levert, kunt u uw toepassing zo configureren dat deze aanmeldingen accepteert van elke Azure Active Directory-Tenant (Azure AD). Deze configuratie heet *het maken van uw toepassing met meerdere tenants*. Gebruikers in een Azure AD-Tenant kunnen zich aanmelden bij uw toepassing nadat deze hebben ingestemd om hun account bij uw toepassing te gebruiken.

Als u een bestaande toepassing hebt met een eigen account systeem of andere soorten aanmeldingen van andere cloud providers ondersteunt, is het toevoegen van Azure AD-aanmelding vanuit een wille keurige Tenant eenvoudig. U hoeft alleen maar uw app te registreren, aanmeldings code toe te voegen via OAuth2, OpenID Connect Connect of SAML en de [knop ' Aanmelden met Microsoft '][AAD-App-Branding] te plaatsen in uw toepassing.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al bekend bent met het bouwen van een toepassing met één Tenant voor Azure AD. Als dat niet het geval is, kunt u beginnen met een van de Quick starts op de [Start pagina voor de ontwikkelaars handleiding][AAD-Dev-Guide].

Er zijn vier stappen om uw toepassing te converteren naar een Azure AD-App voor meerdere tenants:

1. [De registratie van uw toepassing bijwerken naar multi tenant](#update-registration-to-be-multi-tenant)
2. [Uw code bijwerken om aanvragen naar het/veelvoorkomende-eind punt te verzenden](#update-your-code-to-send-requests-to-common)
3. [Uw code bijwerken om meerdere uitgevers waarden te verwerken](#update-your-code-to-handle-multiple-issuer-values)
4. [Meer informatie over de toestemming van gebruikers en beheerders en het maken van passende code wijzigingen](#understand-user-and-admin-consent)

Laten we eens kijken naar elke stap. U kunt ook direct naar het voor beeld gaan met [een multi tenant SaaS-webtoepassing die Microsoft Graph aanroept met behulp van Azure AD en OpenID Connect Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Registratie bijwerken naar multi tenant

Web app/API-registraties in azure AD zijn standaard één Tenant. U kunt uw registratie meerdere tenants maken door de optie **ondersteunde account typen** te vinden in het deel venster **verificatie** van de registratie van uw toepassing in de [Azure Portal][AZURE-portal] en in te stellen op **accounts in elke organisatie Directory**.

Voordat een toepassing kan worden gemaakt met meerdere tenants, moet Azure AD de App-ID-URI van de toepassing globaal uniek zijn. De URI van de app-id is een van de manieren waarop een toepassing wordt geïdentificeerd in protocolberichten. Voor een toepassing met één tenant is het voldoende dat de URI van de app-id uniek is binnen die tenant. Voor een multitenant toepassing moet deze wereldwijd uniek zijn, zodat Azure Active Directory de toepassing in alle tenants kan vinden. Wereldwijde uniekheid wordt afgedwongen door te vereisen dat de URI van de app-id een hostnaam heeft die overeenkomt met een geverifieerd domein van de Azure Active Directory-tenant.

Apps die zijn gemaakt via de Azure Portal, hebben standaard een wereld wijd unieke App-ID-URI die is ingesteld voor het maken van apps, maar u kunt deze waarde wijzigen. Als de naam van uw Tenant bijvoorbeeld contoso.onmicrosoft.com is, zou een geldige App-ID-URI zouden zijn `https://contoso.onmicrosoft.com/myapp` . Als uw Tenant een geverifieerd domein van heeft `contoso.com` , zou er ook een geldige App-ID-URI zijn `https://contoso.com/myapp` . Als de URI van de app-id dit patroon niet volgt, mislukt het instellen van een multitenant toepassing.

## <a name="update-your-code-to-send-requests-to-common"></a>Uw code bijwerken om aanvragen te verzenden naar/veelvoorkomende

Bij een toepassing met één Tenant worden aanmeldings aanvragen verzonden naar het aanmeldings eindpunt van de Tenant. Voor contoso.onmicrosoft.com is het eind punt bijvoorbeeld: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Aanvragen die worden verzonden naar het eind punt van een Tenant kunnen gebruikers (of gasten) in die Tenant aanmelden bij toepassingen in die Tenant.

Met een multi tenant-toepassing kent de toepassing niet de voor zijde van de Tenant van de gebruiker. Daarom kunt u geen aanvragen verzenden naar het eind punt van een Tenant. In plaats daarvan worden aanvragen verzonden naar een eind punt dat multiplext voor alle Azure AD-tenants: `https://login.microsoftonline.com/common`

Wanneer het micro soft Identity-platform een aanvraag ontvangt in het/veelvoorkomende-eind punt, wordt de gebruiker in en als gevolg hiervan gedetecteerd welke Tenant de gebruiker is. Het/veelvoorkomende-eind punt werkt met alle verificatie protocollen die worden ondersteund door Azure AD: OpenID Connect Connect, OAuth 2,0, SAML 2,0 en WS-Federation.

Het aanmeldings antwoord voor de toepassing bevat vervolgens een token dat de gebruiker vertegenwoordigt. De waarde van de verlener in het token geeft aan op welke toepassing de gebruiker zich bevindt. Wanneer een antwoord wordt geretourneerd vanuit het/veelvoorkomende-eind punt, komt de waarde van de verlener in het token overeen met de Tenant van de gebruiker.

> [!IMPORTANT]
> Het/veelvoorkomende-eind punt is geen Tenant en is geen verlener, het is alleen een multiplexer. Wanneer u/veelvoorkomende gebruikt, moet de logica in uw toepassing om tokens te valideren worden bijgewerkt om rekening te houden.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uw code bijwerken om meerdere uitgevers waarden te verwerken

Webtoepassingen en Web-Api's ontvangen en valideren tokens van het micro soft Identity-platform.

> [!NOTE]
> Hoewel systeem eigen client toepassingen tokens van het micro soft Identity-platform aanvragen en ontvangen, kunnen ze ze verzenden naar Api's, waar ze worden gevalideerd. Systeem eigen toepassingen valideren geen toegangs tokens en moeten ze als dekkend behandelen.

Laten we eens kijken hoe tokens die de toepassing ontvangt, worden gevalideerd van het micro soft Identity-platform. Een toepassing met één Tenant heeft normaal gesp roken een eindpunt waarde als:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... en gebruikt deze om een meta gegevens-URL te maken (in dit geval OpenID Connect Connect), zoals:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

voor het downloaden van twee belang rijke stukjes informatie die worden gebruikt voor het valideren van tokens: de handtekening sleutels en de waarde van de verlener van de Tenant.

Elke Azure AD-Tenant heeft een unieke Issuer-waarde van de vorm:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... de GUID-waarde is de naam van de Tenant-ID van de Tenant. Als u de voor gaande meta gegevens koppeling voor hebt geselecteerd `contoso.onmicrosoft.com` , ziet u de waarde van deze verlener in het document.

Wanneer een toepassing met één Tenant een token valideert, wordt de hand tekening van het token gecontroleerd op basis van de handtekening sleutels uit het meta gegevens document. Met deze test kunt u ervoor zorgen dat de naam van de verlener in het token overeenkomt met de waarde die is gevonden in het meta gegevens document.

Omdat het/veelvoorkomende-eind punt niet overeenkomt met een Tenant en geen verlener is, controleert u de waarde van de verlener in de meta gegevens voor/veelvoorkomende deze een URL met sjabloon heeft in plaats van een werkelijke waarde:

```http
https://sts.windows.net/{tenantid}/
```

Daarom kan een toepassing met meerdere tenants geen tokens valideren door te voldoen aan de waarde van de verlener in de meta gegevens met de `issuer` waarde in het token. Een toepassing met meerdere tenants heeft logica nodig om te bepalen welke Issuer-waarden geldig zijn en die niet zijn gebaseerd op het Tenant-ID-gedeelte van de waarde van de verlener.

Als bijvoorbeeld een multi tenant-toepassing alleen aanmelding toestaat vanuit specifieke tenants die zich hebben geregistreerd voor hun service, dan moet de waarde van de verlener of `tid` claim waarde in het token worden gecontroleerd om ervoor te zorgen dat de Tenant zich in de lijst met abonnees bevindt. Als een toepassing met meerdere tenants alleen met individuen werkt en geen toegangs beslissingen maakt op basis van tenants, dan kan de waarde van de verlener worden genegeerd.

In de voor [beelden met meerdere tenants][AAD-Samples-MT]wordt de verlener uitgeschakeld om een Azure AD-Tenant in te scha kelen om zich aan te melden.

## <a name="understand-user-and-admin-consent"></a>Toestemming van gebruiker en beheerder

Een gebruiker kan zich alleen aanmelden bij een toepassing in azure AD als de toepassing wordt weer gegeven in de Tenant van de gebruiker. Zo kan de organisatie acties uitvoeren zoals het Toep assen van een uniek beleid wanneer gebruikers van hun Tenant zich aanmelden bij de toepassing. Voor een toepassing met één Tenant is deze registratie eenvoudiger; Dit is de versie die wordt uitgevoerd wanneer u de toepassing registreert in de [Azure Portal][AZURE-portal].

Voor een toepassing met meerdere tenants is de initiële registratie voor de toepassing in de Azure AD-Tenant die wordt gebruikt door de ontwikkelaar. Wanneer een gebruiker van een andere Tenant zich voor de eerste keer aanmeldt bij de toepassing, vraagt Azure AD ze om toestemming te geven aan de machtigingen die de toepassing heeft aangevraagd. Als ze toestemming geven, wordt een weer gave van de toepassing die een *Service-Principal* wordt genoemd, gemaakt in de Tenant van de gebruiker en kan de aanmelding worden voortgezet. Er wordt ook een delegering in de Directory gemaakt die de toestemming van de gebruiker registreert voor de toepassing. Zie [toepassings objecten en Service-Principal-objecten][AAD-App-SP-Objects]voor meer informatie over de toepassings-en ServicePrincipal-objecten van de toepassing en hoe ze met elkaar zijn verbonden.

![Illustreert de toestemming voor app met één laag][Consent-Single-Tier]

Deze bestemmings ervaring wordt beïnvloed door de machtigingen die de toepassing heeft aangevraagd. Het micro soft Identity-platform ondersteunt twee soorten machtigingen: alleen app en gedelegeerd.

* Een gedelegeerde machtiging verleent een toepassing de mogelijkheid om te fungeren als een aangemelde gebruiker voor een subset van de dingen die de gebruiker kan doen. U kunt bijvoorbeeld een toepassing de gedelegeerde machtiging verlenen voor het lezen van de agenda van de aangemelde gebruiker.
* Een machtiging alleen voor apps wordt rechtstreeks verleend aan de identiteit van de toepassing. U kunt bijvoorbeeld een toepassing de machtiging alleen voor de app verlenen om de lijst met gebruikers in een Tenant te lezen, ongeacht wie zich heeft aangemeld bij de toepassing.

Sommige machtigingen kunnen worden doorgestuurd naar een gewone gebruiker, terwijl anderen toestemming van de Tenant beheerder vereisen.

Zie [de beheerder toestemming werk stroom configureren](../manage-apps/configure-admin-consent-workflow.md)voor meer informatie over de toestemming van de gebruiker en de beheerder.

### <a name="admin-consent"></a>toestemming van de beheerder

Bij app-specifieke machtigingen is er altijd toestemming van een tenantbeheerder nodig. Als uw toepassing een machtiging voor een app aanvraagt en een gebruiker zich probeert aan te melden bij de toepassing, wordt een fout bericht weer gegeven met de melding dat de gebruiker geen toestemming kan geven.

Bepaalde gedelegeerde machtigingen vereisen ook de toestemming van een Tenant beheerder. Het is bijvoorbeeld mogelijk om terug te schrijven naar Azure AD, omdat de aangemelde gebruiker toestemming van de Tenant beheerder nodig heeft. Net als alleen app-machtigingen geldt dat als een gewone gebruiker zich probeert aan te melden bij een toepassing die een gedelegeerde machtiging aanvraagt die toestemming van de beheerder vereist, uw toepassing een fout bericht ontvangt. Of een machtiging beheerders toestemming vereist, wordt bepaald door de ontwikkelaar die de resource heeft gepubliceerd en kan worden gevonden in de documentatie voor de resource. De machtigingen documentatie voor de [Microsoft Graph-API][MSFT-Graph-permission-scopes] geven aan welke machtigingen beheerders toestemming nodig heeft.

Als uw toepassing machtigingen gebruikt waarvoor beheerders toestemming is vereist, moet u een penbeweging hebben zoals een knop of koppeling waar de beheerder de actie kan initiëren. De aanvraag die uw toepassing verzendt voor deze actie is het gebruikelijke OAuth2/OpenID Connect Connect-autorisatie verzoek dat ook de `prompt=admin_consent` query teken reeks parameter bevat. Zodra de beheerder toestemming heeft gegeven en de Service-Principal is gemaakt in de Tenant van de klant, hebben volgende aanmeldings aanvragen de `prompt=admin_consent` para meter niet nodig. Omdat de beheerder heeft vastgesteld dat de aangevraagde machtigingen acceptabel zijn, worden er geen andere gebruikers in de Tenant om vanaf dat moment toestemming gevraagd.

Tenantbeheerders kunnen uitschakelen dat normale gebruikers toestemming kunnen geven voor toepassingen. Als dit wordt uitgeschakeld, is er altijd beheerderstoestemming nodig om een toepassing in een tenant te kunnen gebruiken. Als u uw toepassing wilt testen wanneer de toestemming van de eind gebruiker is uitgeschakeld, kunt u de configuratie-switch vinden in de [Azure Portal][AZURE-portal] in de sectie **[gebruikers instellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** onder **bedrijfs toepassingen**.

De `prompt=admin_consent` para meter kan ook worden gebruikt door toepassingen die machtigingen aanvragen waarvoor geen beheerder toestemming nodig is. Een voor beeld van wanneer de toepassing wordt gebruikt, is een ervaring waarbij de Tenant beheerder één keer meldt en er geen andere gebruikers om toestemming wordt gevraagd van dat punt op.

Als een toepassing beheerders toestemming vereist en een beheerder zich aanmeldt zonder de `prompt=admin_consent` para meter die wordt verzonden, wordt de beheerder **alleen van toepassing op hun gebruikers account**. Gewone gebruikers kunnen zich nog steeds niet aanmelden of toestemming geven voor de toepassing. Deze functie is handig als u de Tenant beheerder de mogelijkheid wilt geven om uw toepassing te verkennen voordat u andere gebruikers toegang verleent.

### <a name="consent-and-multi-tier-applications"></a>Toestemming en toepassingen met meerdere lagen

Uw toepassing heeft mogelijk meerdere lagen, die elk worden vertegenwoordigd door een eigen registratie in azure AD. Bijvoorbeeld een systeem eigen toepassing die een web-API aanroept, of een webtoepassing die een web-API aanroept. In beide gevallen vraagt de client (systeem eigen app of web-app) machtigingen voor het aanroepen van de bron (Web-API). De client kan alleen worden doorgestuurd naar de Tenant van een klant als alle resources waaraan het verzoek is toegewezen, al bestaan in de Tenant van de klant. Als niet aan deze voor waarde wordt voldaan, retourneert Azure AD een fout melding dat de resource eerst moet worden toegevoegd.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Meerdere lagen in één Tenant

Dit kan een probleem zijn als uw logische toepassing bestaat uit twee of meer toepassings registraties, bijvoorbeeld een afzonderlijke client en resource. Hoe wordt de resource eerst in de Tenant van de klant weer geven? Azure AD bestrijkt deze situatie door de client en resource in te scha kelen in één stap. De gebruiker ziet het totaal van de machtigingen die zijn aangevraagd door de client en de resource op de pagina toestemming. Als u dit gedrag wilt inschakelen, moet de toepassings registratie van de resource de App-ID van de client `knownClientApplications` in het [toepassings manifest][AAD-App-Manifest]van de bron bevatten. Bijvoorbeeld:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Dit wordt geïllustreerd in een native client voor het aanroepen van een web-API met meerdere lagen in de sectie [gerelateerde inhoud](#related-content) aan het einde van dit artikel. In het volgende diagram vindt u een overzicht van de toestemming voor een app met meerdere lagen die is geregistreerd in één Tenant.

![Illustreert de instemming met de bekende client-app met meerdere lagen][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Meerdere lagen in meerdere tenants

Een vergelijkbaar geval treedt op als de verschillende lagen van een toepassing in verschillende tenants worden geregistreerd. Denk bijvoorbeeld aan het bouwen van een systeem eigen client toepassing die de Exchange Online API aanroept. Voor het ontwikkelen van de systeem eigen toepassing en later voor het uitvoeren van de systeem eigen toepassing in de Tenant van een klant, moet de service-principal van Exchange Online zijn. In dit geval moet de ontwikkelaar en klant Exchange Online kopen voor de service-principal die in hun tenants moet worden gemaakt.

Als het een API is die door een andere organisatie dan micro soft is gebouwd, moet de ontwikkelaar van de API een manier bieden om hun klanten de toepassing te laten toestemming geven aan de tenants van hun klanten. Het aanbevolen ontwerp is voor de ontwikkelaar van derden om de API te bouwen, zodat deze ook kan worden gebruikt als een webclient voor het implementeren van de registratie. Om dit te doen:

1. Volg de eerdere secties om te controleren of de API de vereisten voor multi tenant-toepassingen registreren/code implementeert.
2. Naast het weer geven van de scopes/rollen van de API, moet u ervoor zorgen dat de registratie de machtiging ' aanmelden en gebruikers profiel lezen ' bevat (standaard).
3. Implementeer een pagina voor aanmelden/aanmelden in de webclient en volg de instructies voor de [beheerder](#admin-consent) .
4. Zodra de gebruiker aan de toepassing heeft gerefereerd, worden de koppelingen Service-Principal en overdracht van toestemming in hun Tenant gemaakt. de systeem eigen toepassing kan tokens voor de API ophalen.

In het volgende diagram vindt u een overzicht van de toestemming voor een app met meerdere lagen die is geregistreerd in verschillende tenants.

![Illustreert de toestemming voor multi-party-apps met meerdere lagen][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Toestemming intrekken

Gebruikers en beheerders kunnen de toestemming voor uw toepassing op elk gewenst moment intrekken:

* Gebruikers trekken de toegang tot afzonderlijke toepassingen in door ze te verwijderen uit de lijst met toepassingen van het [toegangs venster][AAD-Access-Panel] .
* Beheerders trekken de toegang tot toepassingen in door ze te verwijderen in het gedeelte [bedrijfs toepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) van de [Azure Portal][AZURE-portal].

Als een beheerder een toepassing voor alle gebruikers in een Tenant toestuurt, kunnen gebruikers de toegang niet afzonderlijk intrekken. Alleen de beheerder kan de toegang intrekken en alleen voor de hele toepassing.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Toepassingen voor meerdere tenants en toegangs tokens in de cache

Toepassingen met meerdere tenants kunnen ook toegangs tokens krijgen om Api's aan te roepen die door Azure AD worden beveiligd. Een veelvoorkomende fout bij het gebruik van de micro soft Authentication Library (MSAL) met een toepassing met meerdere tenants is om eerst een token aan te vragen voor een gebruiker met behulp van/veelvoorkomende, een reactie te ontvangen en vervolgens een volgend token aan te vragen voor diezelfde gebruiker met behulp van/common. Omdat het antwoord van Azure AD afkomstig is van een Tenant, niet/veelvoorkomende, MSAL het token in de cache van de Tenant opgeslagen. De volgende aanroep van/veelvoorkomende voor het ophalen van een toegangs token voor de gebruiker heeft de cache vermelding missen en de gebruiker wordt gevraagd zich opnieuw aan te melden. Om te voor komen dat de cache ontbreekt, moet u ervoor zorgen dat de volgende aanroepen voor een al aangemelde gebruiker worden gemaakt aan het eind punt van de Tenant.

## <a name="related-content"></a>Gerelateerde inhoud

* [Voor beeld van multi tenant-toepassing](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Huisstijlrichtlijnen voor apps][AAD-App-Branding]
* [Toepassings objecten en Service-Principal-objecten][AAD-App-SP-Objects]
* [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps]
* [Overzicht van het instemming raamwerk][AAD-Consent-Overview]
* [Microsoft Graph API-machtigings bereik][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een toepassing bouwt die kan worden aangemeld bij een gebruiker vanuit een Azure AD-Tenant. Nadat u eenmalige Sign-On (SSO) tussen uw app en Azure AD hebt ingeschakeld, kunt u uw toepassing ook bijwerken om toegang te krijgen tot Api's die beschikbaar worden gesteld door micro soft-resources, zoals Microsoft 365. Zo kunt u een persoonlijke ervaring bieden in uw toepassing, zoals het weer geven van contextuele informatie aan de gebruikers, zoals de profiel afbeelding of de volgende agenda-afspraak.

Ga voor meer informatie over het maken van API-aanroepen naar Azure AD en Microsoft 365 services zoals Exchange, share point, OneDrive, OneNote en meer naar [Microsoft Graph-API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken