---
title: Verklarende woordenlijst voor ontwikkelaars van Microsoft Identity Platform | Azure
description: Een lijst met termen voor veelgebruikte concepten en functies voor ontwikkelaars van Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 930341b60f785c2c618be4ee235225519a08aaa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530061"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Woordenlijst voor ontwikkelaars van Microsoft Identity Platform

Dit artikel bevat definities voor enkele van de belangrijkste concepten en terminologie voor ontwikkelaars, die handig zijn wanneer u meer te weten komt over het ontwikkelen van toepassingen met behulp van het Microsoft Identity Platform.

## <a name="access-token"></a>toegang token

Een type beveiliging [token dat is](#security-token) uitgegeven door een autorisatieserver en wordt gebruikt door een [clienttoepassing](#client-application) om toegang te krijgen tot een [beveiligde resourceserver.](#resource-server) [](#authorization-server) Normaal gesproken in de vorm van [een JSON Web Token (JWT)][JWT]beeert het token de autorisatie die door de [resource-eigenaar](#resource-owner)aan de client is verleend voor een aangevraagd toegangsniveau. Het token bevat alle toepasselijke [claims](#claim) over het onderwerp, waardoor de clienttoepassing deze kan gebruiken als een vorm van referentie bij het openen van een bepaalde resource. Dit elimineert ook de noodzaak voor de resource-eigenaar om referenties aan de client weer te geven.

Toegangstokens zijn slechts korte tijd geldig en kunnen niet worden ingetrokken. Een autorisatieserver kan ook een [vernieuwings-token uitgeven](#refresh-token) wanneer het toegangs token wordt uitgegeven. Vernieuwingstokens worden doorgaans alleen verstrekt aan vertrouwelijke clienttoepassingen.

Toegangstokens worden soms aangeduid als 'Gebruiker+app' of 'Alleen-app', afhankelijk van de referenties die worden weergegeven. Wanneer een clienttoepassing bijvoorbeeld het volgende gebruikt:

* [Autorisatiecode verleent](#authorization-grant), de eindgebruiker verifieert eerst als de resource-eigenaar, en delegeert autorisatie naar de client voor toegang tot de resource. De client wordt later geverifieerd bij het verkrijgen van het toegangs token. Het token kan soms specifieker worden aangeduid als een token 'Gebruiker+app', omdat het zowel de gebruiker vertegenwoordigt die de clienttoepassing heeft geautoriseerd als de toepassing.
* [De](#authorization-grant)autorisatie 'Clientreferenties' verleent , de client biedt de enige verificatie, die functioneert zonder de verificatie/autorisatie van de resource-eigenaar, zodat het token soms kan worden aangeduid als een 'alleen-app'-token.

Zie de [Naslaginformatie over Het Microsoft Identity Platform-token][AAD-Tokens-Claims] voor meer informatie.

## <a name="application-id-client-id"></a>toepassings-id (client-id)

De unieke id die Azure AD uit geeft aan een toepassingsregistratie die een specifieke toepassing en de bijbehorende configuraties identificeert. Deze toepassings-id[(client-id)](https://tools.ietf.org/html/rfc6749#page-15)wordt gebruikt bij het uitvoeren van verificatieaanvragen en wordt tijdens de ontwikkeling aan de verificatiebibliotheken geleverd. De toepassings-id (client-id) is geen geheim.

## <a name="application-manifest"></a>toepassingsmanifest

Een functie die wordt geleverd door [de Azure Portal][AZURE-portal], die een JSON-weergave van de identiteitsconfiguratie van de toepassing produceert, die wordt gebruikt als mechanisme voor het bijwerken van de bijbehorende Application- en [][Graph-App-Resource] [ServicePrincipal-entiteiten.][Graph-Sp-Resource] Zie Understanding the Azure Active Directory application manifest (Inzicht in [Azure Active Directory toepassingsmanifest)][AAD-App-Manifest] voor meer informatie.

## <a name="application-object"></a>toepassingsobject

Wanneer u een toepassing in de Azure Portal registreert/bij [werkt,][AZURE-portal]maakt/werkt de portal zowel een toepassingsobject als een bijbehorend [service-principal-object](#service-principal-object) voor die tenant aan. Het *toepassingsobject* definieert *de* identiteitsconfiguratie van de toepassing globaal (voor alle tenants waar het toegang heeft), met een sjabloon van waaruit de bijbehorende service-principalobjecten worden afgeleid voor lokaal gebruik tijdens run-time (in een specifieke tenant).

Zie Toepassings- en [service-principalobjecten voor meer informatie.][AAD-App-SP-Objects]

## <a name="application-registration"></a>toepassingsregistratie

Als u wilt toestaan dat een toepassing kan worden geïntegreerd met en identiteits- en toegangsbeheerfuncties kan delegeren aan Azure AD, moet deze worden geregistreerd bij een Azure [AD-tenant.](#tenant) Wanneer u uw toepassing registreert bij Azure AD, biedt u een identiteitsconfiguratie voor uw toepassing, zodat deze kan worden geïntegreerd met Azure AD en functies zoals:

* Robuust beheer van single Sign-On met Behulp van Azure AD Identity Management en [OpenID Connect-protocol][OpenIDConnect]
* Brokered toegang tot [beveiligde bronnen door](#resource-server) [clienttoepassingen](#client-application), via OAuth 2.0-autorisatieserver [](#authorization-server)
* [Toestemmingskader voor](#consent) het beheren van clienttoegang tot beveiligde resources, op basis van de autorisatie van de resource-eigenaar.

Zie [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps] voor meer informatie.

## <a name="authentication"></a>verificatie

Het uitdagen van een partij om legitieme referenties, die de basis vormen voor het maken van een beveiligingsprincipaal die moet worden gebruikt voor identiteits- en toegangsbeheer. Tijdens een [OAuth2-autorisatietoekenning](#authorization-grant) bijvoorbeeld vult de partij die de rol van [resource-eigenaar](#resource-owner) of [clienttoepassing](#client-application)invult, afhankelijk van de gebruikte toekenning.

## <a name="authorization"></a>autorisatie

Het verlenen van een geverifieerde beveiligingsprincipaal machtiging om iets te doen. Er zijn twee primaire gebruiksscenario's in het Azure AD-programmeermodel:

* Tijdens een [OAuth2-autorisatieverleningsstroom:](#authorization-grant) wanneer de [resource-eigenaar](#resource-owner) autorisatie verleent aan de [clienttoepassing,](#client-application)zodat de client toegang heeft tot de resources van de resource-eigenaar.
* Tijdens resourcetoegang door de client: zoals geïmplementeerd door de [resourceserver,](#resource-server)met behulp van de [claimwaarden](#claim) die aanwezig zijn in het toegangs [token](#access-token) om op basis daarvan beslissingen te nemen over toegangsbeheer.

## <a name="authorization-code"></a>autorisatiecode

Een kortdurfd 'token' dat door het autorisatie-eindpunt wordt verstrekt aan een [clienttoepassing](#client-application) [als](#authorization-endpoint)onderdeel van de stroom 'autorisatiecode', verleent een van de vier OAuth2-autorisaties . [](#authorization-grant) De code wordt geretourneerd naar de clienttoepassing als reactie op verificatie van een [resource-eigenaar,](#resource-owner)wat aangeeft dat de resource-eigenaar autorisatie heeft gedelegeerd voor toegang tot de aangevraagde resources. Als onderdeel van de stroom wordt de code later ingewisseld voor een [toegangs token](#access-token).

## <a name="authorization-endpoint"></a>autorisatie-eindpunt

Een van de eindpunten die door de autorisatieserver wordt [geïmplementeerd,](#authorization-server) [](#authorization-grant) wordt gebruikt om te communiceren met de [resource-eigenaar](#resource-owner) om een autorisatietoekenning te verlenen tijdens een OAuth2-autorisatieverleningsstroom. Afhankelijk van de gebruikte stroom voor autorisatietoekenning, kan de opgegeven toekenning variëren, met inbegrip van een autorisatiecode [of beveiliging token](#security-token). [](#authorization-code)

Zie de secties Autorisatietoekenningen en autorisatie-eindpunten van de OAuth2-specificatie en de [OpenIDConnect-specificatie][OpenIDConnect-AuthZ-Endpoint] voor meer informatie. [][OAuth2-AuthZ-Grant-Types] [][OAuth2-AuthZ-Endpoint]

## <a name="authorization-grant"></a>autorisatie verlenen

Een referentie die staat voor [de autorisatie van](#resource-owner) de resource-eigenaar [voor](#authorization) toegang tot de beveiligde resources, verleend aan een [clienttoepassing](#client-application). Een clienttoepassing kan een van de vier toekenningstypen gebruiken die zijn gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] om een toekenning te verkrijgen, afhankelijk van het clienttype/de vereisten: 'autorisatiecode verlenen', 'clientreferenties verlenen', 'impliciete toekenning' en 'wachtwoordreferenties voor resource-eigenaar verlenen'. De referentie die aan de client wordt geretourneerd, is een toegangs [token](#access-token)of een autorisatiecode [(later](#authorization-code) uitgewisseld voor een toegangs token), afhankelijk van het gebruikte type autorisatietoeken.

## <a name="authorization-server"></a>autorisatieserver

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], de server die verantwoordelijk is voor het verlenen van toegangstokens aan de [client](#client-application) nadat de eigenaar van de [resource](#resource-owner) is authenticeerd en de autorisatie is verkrijgen. Een [clienttoepassing communiceert](#client-application) tijdens runtime met [](#authorization-endpoint) de autorisatieserver via de autorisatie- en [token-eindpunten,](#token-endpoint) in overeenstemming met de door OAuth2 gedefinieerde [autorisatie verleent.](#authorization-grant)

In het geval van de integratie van de microsoft-identiteitsplatformtoepassing implementeert het Microsoft-identiteitsplatform de autorisatieserverfunctie voor Azure AD-toepassingen en Api's van Microsoft-service, bijvoorbeeld [Microsoft Graph API's.][Microsoft-Graph]

## <a name="claim"></a>Vordering

Een [beveiliging token](#security-token) bevat claims, die beweringen over één entiteit (zoals een [clienttoepassing](#client-application) of [resource-eigenaar)](#resource-owner)bieden aan een andere entiteit (zoals de [resourceserver](#resource-server)). Claims zijn naam/waarde-paren die feiten over het tokenonderwerp doorgeven (bijvoorbeeld de beveiligingsprincipaal die is geverifieerd door de [autorisatieserver](#authorization-server)). De claims in een bepaald token zijn afhankelijk van verschillende variabelen, waaronder het type token, het type referentie dat wordt gebruikt om het onderwerp te verifiëren, de configuratie van de toepassing, enzovoort.

Zie de [naslaginformatie over het Microsoft Identity Platform-token][AAD-Tokens-Claims] voor meer informatie.

## <a name="client-application"></a>clienttoepassing

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een toepassing die namens de resource-eigenaar beveiligde resourceaanvragen [doet.](#resource-owner) De term 'client' impliceert geen specifieke kenmerken van hardware-implementatie (bijvoorbeeld of de toepassing wordt uitgevoerd op een server, een desktop of andere apparaten).

Een clienttoepassing [vraagt](#authorization) autorisatie aan van een resource-eigenaar om deel te nemen aan een OAuth2-autorisatieverleningsstroom en heeft namens de resource-eigenaar toegang tot API's/gegevens. [](#authorization-grant) Het OAuth2 Authorization Framework definieert twee typen [clients,][OAuth2-Client-Types]'vertrouwelijk' en 'openbaar', op basis van de mogelijkheid van de client om de vertrouwelijkheid van de referenties te handhaven. Toepassingen kunnen een [webclient (vertrouwelijk)](#web-client) implementeren die wordt uitgevoerd op een webserver, een systeemeigen [client (openbaar)](#native-client) die is geïnstalleerd op een apparaat of een client op basis van een gebruikersagent [(openbaar)](#user-agent-based-client) die wordt uitgevoerd in de browser van een apparaat.

## <a name="consent"></a>Toestemming

Het proces waarbij een [resource-eigenaar autorisatie](#resource-owner) verleent [](#permissions)aan een [clienttoepassing](#client-application)om namens de resource-eigenaar toegang te krijgen tot beveiligde resources onder specifieke machtigingen. Afhankelijk van de machtigingen die door de client worden aangevraagd, wordt een beheerder of gebruiker gevraagd om respectievelijk toegang te verlenen tot de organisatie-/individuele gegevens. In een scenario [met meerdere tenants](#multi-tenant-application) wordt de [service-principal](#service-principal-object) van de toepassing ook vastgelegd in de tenant van de gebruiker die toestemming geeft.

Zie [toestemmings framework](consent-framework.md) voor meer informatie.

## <a name="id-token"></a>Id-token

[](#authorization-server) Een [OpenID Connect-beveiliging token](#security-token) dat wordt geleverd door het autorisatie-eindpunt van een autorisatieserver, dat [claims](#claim) bevat die betrekking hebben op de verificatie van de [broneigenaar van](#resource-owner)een eindgebruiker. [][OpenIDConnect-ID-Token] [](#authorization-endpoint) Net als een toegangstoken worden id-tokens ook weergegeven als een digitaal JSON Web Token [(JWT).][JWT] In tegenstelling tot een toegangs token worden de claims van een id-token echter niet gebruikt voor doeleinden met betrekking tot resourcetoegang en specifiek toegangsbeheer.

Zie de [naslaginformatie over het Microsoft Identity Platform-token][AAD-Tokens-Claims] voor meer informatie.

## <a name="microsoft-identity-platform"></a>Microsoft-identiteitsplatform

Het Microsoft Identity Platform is een evolutie van de Azure Active Directory (Azure AD)-identiteitsservice en het ontwikkelaarsplatform. Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft Graph, andere Microsoft-API's of API's die door ontwikkelaars zijn gemaakt. Het is een volledig platform dat bestaat uit een verificatieservice, bibliotheken, toepassingsregistratie en -configuratie, volledige documentatie voor ontwikkelaars, codevoorbeelden en andere inhoud voor ontwikkelaars. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect.

## <a name="multi-tenant-application"></a>toepassing met meerdere tenants

Een toepassingsklasse waarmee gebruikers [](#consent) zich kunnen aanmelden en toestemming kunnen geven die zijn ingericht in een Azure [AD-tenant,](#tenant)met inbegrip van andere tenants dan de tenant waarin de client is geregistreerd. [Native clienttoepassingen](#native-client) zijn standaard multiten tenants, terwijl [webclient-](#web-client) en [webresource-/API-toepassingen](#resource-server) de mogelijkheid hebben om te kiezen tussen één of meerdere tenants. Een webtoepassing die daarentegen is geregistreerd als één tenant, staat alleen aanmeldingen toe vanuit gebruikersaccounts die zijn ingericht in dezelfde tenant als de tenant waarin de toepassing is geregistreerd.

Zie [Een Azure AD-gebruiker aanmelden met behulp van het toepassingspatroon voor][AAD-Multi-Tenant-Overview] meerdere tenants voor meer informatie.

## <a name="native-client"></a>native client

Een type [clienttoepassing dat](#client-application) systeemeigen is geïnstalleerd op een apparaat. Omdat alle code wordt uitgevoerd op een apparaat, wordt deze beschouwd als een 'openbare' client vanwege het onvermogen om referenties privé/vertrouwelijk op te slaan. Zie [OAuth2-clienttypen en -profielen][OAuth2-Client-Types] voor meer informatie.

## <a name="permissions"></a>permissions

Een [clienttoepassing krijgt](#client-application) toegang tot een [resourceserver door](#resource-server) machtigingsaanvragen te declareren. Er zijn twee typen beschikbaar:

* Gedelegeerde machtigingen, die [](#scopes) toegang op basis van een bereik opgeven met behulp van gedelegeerde autorisatie van de aangemelde [resource-eigenaar,](#resource-owner)worden tijdens run time aan de resource gepresenteerd als ['scp'-claims](#claim) in het toegangs [token](#access-token)van de client.
* Toepassingsmachtigingen, die op rollen gebaseerde toegang opgeven met behulp van de referenties/identiteit van de clienttoepassing, worden tijdens run time aan de resource gepresenteerd als ['rollen'-claims](#roles) in het toegangsken van de client. [](#claim)

Ze komen ook naar boven tijdens [het toestemmingsproces,](#consent) waardoor de beheerder of resource-eigenaar de mogelijkheid krijgt om de client toegang te verlenen/weigeren tot resources in hun tenant.

Machtigingsaanvragen worden geconfigureerd op de pagina API-machtigingen voor een toepassing in de [Azure Portal][AZURE-portal]door de gewenste 'Gedelegeerde machtigingen' en 'Toepassingsmachtigingen' te selecteren (voor de laatste is lidmaatschap van de rol Globale beheerder vereist).  Omdat een [openbare client](#client-application) referenties niet veilig kan onderhouden, kan deze alleen gedelegeerde machtigingen aanvragen, terwijl een vertrouwelijke [client](#client-application) de mogelijkheid heeft om zowel gedelegeerde als toepassingsmachtigingen aan te vragen. Het toepassingsobject van [de client](#application-object) slaat de gedeclareerde machtigingen op in de [eigenschap requiredResourceAccess.][Graph-App-Resource]

## <a name="refresh-token"></a>token vernieuwen

Een type beveiliging token dat is uitgegeven door een autorisatieserver [en](#authorization-server)wordt gebruikt door een [clienttoepassing](#client-application) om een nieuw toegang [token](#security-token) aan te vragen voordat het toegangs token verloopt. [](#access-token) Doorgaans in de vorm van [een JSON Web Token (JWT).][JWT]

In tegenstelling tot toegangstokens kunnen vernieuwingstokens worden ingetrokken. Als een clienttoepassing probeert een nieuw toegang token aan te vragen met behulp van een vernieuwings token dat is ingetrokken, weigert de autorisatieserver de aanvraag en heeft de clienttoepassing niet langer toestemming om toegang te krijgen tot de [resourceserver](#resource-server) namens de [broneigenaar](#resource-owner).

## <a name="resource-owner"></a>resource-eigenaar

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een entiteit die toegang kan verlenen tot een beveiligde resource. Wanneer de resource-eigenaar een persoon is, wordt deze aangeduid als een eindgebruiker. Wanneer een [clienttoepassing](#client-application) bijvoorbeeld toegang wil tot het postvak van een gebruiker via de [Microsoft Graph-API,][Microsoft-Graph]is hiervoor toestemming van de resource-eigenaar van het postvak vereist.

## <a name="resource-server"></a>resourceserver

Zoals gedefinieerd door het [OAuth2 Authorization Framework][OAuth2-Role-Def], een server die beveiligde resources host, die in staat is om beveiligde resourceaanvragen te accepteren en erop te reageren door [clienttoepassingen](#client-application) die een toegangsteken [presenteren.](#access-token) Dit wordt ook wel een beveiligde resourceserver of resourcetoepassing genoemd.

Een resourceserver maakt API's beschikbaar en dwingt toegang tot de beveiligde resources af via [bereiken](#scopes) en rollen [,](#roles)met behulp van het OAuth 2.0 Authorization Framework. Voorbeelden hiervan zijn [de Microsoft Graph-API][Microsoft-Graph] die toegang biedt tot azure AD-tenantgegevens en de Microsoft 365-API's die toegang bieden tot gegevens zoals e-mail en agenda.

Net als bij een clienttoepassing wordt de identiteitsconfiguratie van de resourcetoepassing tot stand gebracht [via](#application-registration) registratie in een Azure AD-tenant, waardoor zowel de toepassing als het service-principal-object worden gebruikt. Sommige door Microsoft geleverde API's, zoals de Microsoft Graph API, hebben vooraf geregistreerde service-principals die tijdens het inrichten beschikbaar zijn gesteld in alle tenants.

## <a name="roles"></a>rollen

Net [als bereiken](#scopes)bieden rollen een manier voor een [resourceserver om](#resource-server) de toegang tot de beveiligde resources te beheren. Er zijn twee typen: een gebruikersrol implementeert op rollen gebaseerd toegangsbeheer voor gebruikers/groepen die toegang tot de resource nodig hebben, terwijl een toepassingsrol hetzelfde implementeert voor [clienttoepassingen](#client-application) die toegang nodig hebben.

Rollen zijn door resources gedefinieerde tekenreeksen (bijvoorbeeld 'Onkosten goedkeurder', 'Alleen-lezen', 'Directory.ReadWrite.All'), beheerd in de [Azure Portal][AZURE-portal] via het toepassingsmanifest van de resource en opgeslagen in de [eigenschap appRoles][Graph-Sp-Resource]van de resource. [](#application-manifest) De Azure Portal wordt ook gebruikt om gebruikers toe te wijzen [](#permissions) aan gebruikersrollen en om clienttoepassingsmachtigingen te configureren voor toegang tot een toepassingsrol.

Zie Machtigingsbereiken voor meer informatie over de toepassingsrollen die beschikbaar worden gemaakt door Microsoft Graph [Graph API][Graph-Perm-Scopes]API. Zie Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal voor [een stapsgewijs implementatievoorbeeld.][AAD-RBAC]

## <a name="scopes"></a>bereiken

Net [als rollen](#roles)bieden bereiken een manier voor een [resourceserver om](#resource-server) de toegang tot de beveiligde resources te beheren. Bereiken worden gebruikt voor het implementeren van [toegangsbeheer][OAuth2-Access-Token-Scopes] op basis van een bereik voor een [clienttoepassing](#client-application) die gedelegeerde toegang tot de resource heeft gekregen van de eigenaar.

Bereiken zijn door resources gedefinieerde tekenreeksen (bijvoorbeeld 'Mail.Read', 'Directory.ReadWrite.All'), beheerd [](#application-manifest)in de [Azure Portal][AZURE-portal] via het toepassingsmanifest van de resource en opgeslagen in de eigenschap [oauth2Permissions][Graph-Sp-Resource]van de resource. De Azure Portal wordt ook gebruikt om gedelegeerde machtigingen voor clienttoepassing [te configureren](#permissions) voor toegang tot een bereik.

Een best practice naamconventie is het gebruik van de indeling resource.operation.constraint. Zie machtigingsbereiken voor Microsoft Graph gedetailleerde beschrijving van de Microsoft Graph [Graph API-API.][Graph-Perm-Scopes] Zie naslaginformatie over API Microsoft 365 machtigingen voor Microsoft 365 bereik dat beschikbaar wordt gemaakt door [Microsoft 365 services.][O365-Perm-Ref]

## <a name="security-token"></a>Beveiligingstoken

Een ondertekend document met claims, zoals een OAuth2-token of SAML 2.0-verklaring. Voor een OAuth2-autorisatietoeken zijn een toegangstoken (OAuth2), vernieuwingstoken en een [](#access-token) [](#authorization-grant) [id-token](#refresh-token)typen beveiligingstokens, die allemaal worden geïmplementeerd als [een JSON Web Token (JWT).][JWT] [](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)

## <a name="service-principal-object"></a>service-principal-object

Wanneer u een toepassing in de Azure Portal registreert/bij [werkt,][AZURE-portal]worden in de portal zowel een toepassingsobject als een bijbehorend service-principal-object voor die tenant gemaakt/bijgewerkt. [](#application-object) Het *toepassingsobject* definieert de identiteitsconfiguratie van de toepassing globaal (voor alle tenants waaraan de gekoppelde toepassing  toegang is verleend) en is de sjabloon van waaruit de bijbehorende service-principal-objecten zijn afgeleid voor lokaal gebruik tijdens run time (in een specifieke tenant).

Zie Toepassings- en [service-principalobjecten voor meer informatie.][AAD-App-SP-Objects]

## <a name="sign-in"></a>aanmelden

Het proces van een [clienttoepassing](#client-application) initieert de verificatie van eindgebruikers en legt de gerelateerde status vast, om een beveiliging [token](#security-token) te verkrijgen en het bereik van de toepassingssessie tot die status te brengen. De status kan artefacten bevatten, zoals gebruikersprofielgegevens en informatie die is afgeleid van tokenclaims.

De aanmeldingsfunctie van een toepassing wordt doorgaans gebruikt om eenmalige aanmelding (SSO) te implementeren. Het kan ook worden voorafgegaan door een 'aanmeldingsfunctie', als het toegangspunt voor een eindgebruiker om toegang te krijgen tot een toepassing (bij de eerste aanmelding). De aanmeldingsfunctie wordt gebruikt voor het verzamelen en persistent maken van aanvullende statussen die specifiek zijn voor de gebruiker. Mogelijk is toestemming [van de gebruiker vereist.](#consent)

## <a name="sign-out"></a>afmelden

Het proces van het niet-autoriëren van een eindgebruiker, [](#client-application) het loskoppelen van de gebruikerstoestand die is gekoppeld aan de clienttoepassingssessie [tijdens het aanmelden](#sign-in)

## <a name="tenant"></a>tenant

Een exemplaar van een Azure AD-directory wordt een Azure AD-tenant genoemd. Het biedt verschillende functies, waaronder:

* een registerservice voor geïntegreerde toepassingen
* verificatie van gebruikersaccounts en geregistreerde toepassingen
* REST-eindpunten die zijn vereist voor de ondersteuning van verschillende [](#authorization-endpoint)protocollen, waaronder OAuth2 en SAML, waaronder het autorisatie-eindpunt, [het token-eindpunt](#token-endpoint) en het 'algemene' eindpunt dat wordt gebruikt door toepassingen met meerdere [tenants.](#multi-tenant-application)

Azure AD-tenants worden tijdens de aanmelding gemaakt/gekoppeld aan Azure- en Microsoft 365-abonnementen, waardoor identity & Access Management-functies voor het abonnement worden bieden. Azure-abonnementsbeheerders kunnen ook extra Azure AD-tenants maken via de Azure Portal. Zie [Een tenant Azure Active Directory voor][AAD-How-To-Tenant] meer informatie over de verschillende manieren waarop u toegang kunt krijgen tot een tenant. Zie Een Azure-abonnement koppelen of toevoegen aan uw [Azure Active Directory-tenant][AAD-How-Subscriptions-Assoc] voor meer informatie over de relatie tussen abonnementen en een Azure AD-tenant en voor instructies over het koppelen of toevoegen van een abonnement aan een Azure AD-tenant.

## <a name="token-endpoint"></a>token-eindpunt

Een van de eindpunten die door de autorisatieserver is [geïmplementeerd ter](#authorization-server) ondersteuning van OAuth2-autorisatie [verleent](#authorization-grant). Afhankelijk van de toekenning kan deze worden gebruikt voor het verkrijgen van een toegangs [token](#access-token) (en het bijbehorende vernieuwings-token) voor een [client](#client-application)of [id-token](#id-token) wanneer het wordt gebruikt met het [OpenID Connect-protocol.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Client op basis van gebruikersagent

Een type [clienttoepassing](#client-application) die code downloadt van een webserver en wordt uitgevoerd binnen een gebruikersagent (bijvoorbeeld een webbrowser), zoals een toepassing met één pagina (SPA). Omdat alle code wordt uitgevoerd op een apparaat, wordt deze beschouwd als een 'openbare' client vanwege het onvermogen om referenties privé/vertrouwelijk op te slaan. Zie [OAuth2-clienttypen en -profielen voor meer informatie.][OAuth2-Client-Types]

## <a name="user-principal"></a>user principal

Vergelijkbaar met de manier waarop een service-principal-object wordt gebruikt om een toepassings-exemplaar weer te geven, is een user principal-object een ander type beveiligingsprincipaal, dat een gebruiker vertegenwoordigt. Het Microsoft Graph [Resourcetype][Graph-User-Resource] gebruiker definieert het schema voor een gebruikersobject, inclusief gebruikerseigenschappen zoals voor- en achternaam, user principal name, lidmaatschap van directory-rol, enzovoort. Dit biedt de configuratie van de gebruikersidentiteit voor Azure AD voor het maken van een gebruikers-principal tijdens run-time. De gebruikersprincipaal wordt gebruikt om een geverifieerde gebruiker te vertegenwoordigen voor een enkele aanmelding, het [vastleggen](#consent) van toestemmingsdelegatie, het nemen van beslissingen voor toegangsbeheer, enzovoort.

## <a name="web-client"></a>webclient

Een type [clienttoepassing dat](#client-application) alle code op een webserver uitvoert en kan functioneren als een 'vertrouwelijke' client door de referenties veilig op de server op te slaan. Zie [OAuth2-clienttypen en -profielen voor meer informatie.][OAuth2-Client-Types]

## <a name="next-steps"></a>Volgende stappen

De Ontwikkelaarshandleiding voor Het Microsoft-identiteitsplatform is de landingspagina die u kunt gebruiken [][AAD-How-To-Integrate] voor alle onderwerpen met betrekking tot microsoft-identiteitsplatformontwikkeling, met inbegrip van een overzicht van de integratie van toepassingen en de basisbeginselen van verificatie van het [Microsoft-identiteitsplatform][AAD-Auth-Scenarios]en ondersteunde [verificatiescenario's.][AAD-Dev-Guide] U kunt ook codevoorbeelden vinden & zelfstudies over hoe u snel aan de hand kunt gaan op [GitHub.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Gebruik de volgende opmerkingensectie om feedback te geven en om deze inhoud te verfijnen en vorm te geven, inclusief aanvragen voor nieuwe definities of het bijwerken van bestaande definities.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/rfc7519
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
